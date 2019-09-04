---
id: 2515
title: Citrix Storefront â€“ Adventures in customization â€“ Customization breaks in internet explorer
date: 2017-07-14T12:38:02-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2515
permalink: /2017/07/14/citrix-storefront-adventures-in-customization-customization-breaks-in-internet-explorer/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2017/07/CorrectStoreFront.mp4
    191
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2017/07/IE_Broken.mp4
    183
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2017/07/Chrome_WithIEUserAgent.mp4
    196
    video/mp4
    
image: /wp-content/uploads/2017/07/PausedInDebugger.png
categories:
  - Blog
tags:
  - Citrix
  - Storefront
---
I ran into a frustrating issue [with my last post (changing the clientname based on the application)](https://theorypc.ca/2017/07/13/citrix-storefront-adventures-in-customization-assign-a-custom-clientname-to-an-application/). Â The issue was, it wasn&#8217;t working! Â Sometimes! Â On some versions of IE! Â On some Operating Systems!

WTF.

On Windows 10, IE11 or Edge it worked fine. Â On Windows 7 or Server 2008 R2 with IE11 it worked&#8230; Â SOMETIMES.

I started to dig into what was happening. Â Why wasn&#8217;t the ClientName being set consistently?

It turns out that the code path for the &#8220;StoreFront Customization SDK&#8221; (at least the ICA file modification portion) operates when a request is made to [&#8220;GetLaunchStatus&#8221;](https://citrix.github.io/storefront-sdk/requests/#ica-launch). Â A working application launch from StoreFront looks like this:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2515-25" width="1140" height="351" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/07/CorrectStoreFront.mp4?_=25" /><a href="http://theorypc.ca/wp-content/uploads/2017/07/CorrectStoreFront.mp4">http://theorypc.ca/wp-content/uploads/2017/07/CorrectStoreFront.mp4</a></video>
</div>

Notice when I click the application icon, TWO requests are made. Â The first one, GetLaunchStatus contains the headers that is passed to the SDK customization plugin, the second is a hidden iFrame to pull the ICA file.

Now, if I login to a server or PC that I&#8217;ve never logged into before (so that I have a fresh profile) and launch IE and immediately go to Storefront, this is the result:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2515-26" width="1140" height="351" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/07/IE_Broken.mp4?_=26" /><a href="http://theorypc.ca/wp-content/uploads/2017/07/IE_Broken.mp4">http://theorypc.ca/wp-content/uploads/2017/07/IE_Broken.mp4</a></video>
</div>

Notice it only goes to &#8220;LaunchIca&#8221;. Â Because of this my ClientName is not modified.

To validate the theory a bit further that something in Storefront maybe causing my issue, I opened up Chrome and changed it&#8217;s UserAgent to be identical of IE11 on Server 2008 R2:

<pre class="lang:default decode:true ">Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko</pre>

And what happened?

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2515-27" width="1140" height="351" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/07/Chrome_WithIEUserAgent.mp4?_=27" /><a href="http://theorypc.ca/wp-content/uploads/2017/07/Chrome_WithIEUserAgent.mp4">http://theorypc.ca/wp-content/uploads/2017/07/Chrome_WithIEUserAgent.mp4</a></video>
</div>

Only LaunchIca is called. Â We have a reproducible failure.

As I was digging into the Citrix code to launch the &#8220;GetLaunchStatus&#8221; URL I found it would &#8220;skip&#8221; going to URL under a series of conditions, one of them relates to checking for the presence of IE (CTXS.Device.isIE). Â I found two candidate functions that maybe causing this failure, and I think this one is the most likely:

<pre class="lang:js decode:true">function b(a, c) {
        function k(f, e, l) {
            if (l.getResponseHeader(CTXS.CHALLENGE_HEADER))
                CTXS.trace("prepareLaunch: challenged"),
                CTXS.Authentication.isUserAuthenticated() && (h(a, "Unauthorized", !1, c),
                CTXS.Events.publish(CTXS.Events.resources.challenged));
            else
                switch (f.status) {
                case "success":
                    a.delayedLaunchInProgress = !1;
                    e = {};
                    CTXS.Device.isIE() && CTXS.ClientManager.usingNativeClientInInternetZone() ? (g(a, CTXS.LAUNCH_SUCCESS),
                    g(a, CTXS.LAUNCH_READY),
                    a.isLaunchReady = !0) : (CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER && (e.ticket = f.fileFetchTicket,
                    e.staTicket = f.fileFetchStaTicket,
                    e.fileFetchUrl = f.fileFetchUrl,
                    e.serverProtocolVersion = f.serverProtocolVersion),
                    d(a, c, e));
                    break;
                case "retry":
                    a.delayedLaunchInProgress = !0;
                    setTimeout(function() {
                        b(a, c)
                    }, 1E3 * f.pollTimeout);
                    break;
                default:
                    CTXS.trace("prepareLaunch: unexpected response from launchStatus page"),
                    h(a, f.errorId, f.suggestRestart, c)
                }
        }
        function f(b, g) {
            CTXS.trace("errorHandler: failed to download launchStatus page, HTTP status: " + b.status);
            b.getResponseHeader(CTXS.CHALLENGE_HEADER) && (CTXS.trace("prepareLaunch: challenged"),
            CTXS.Authentication.isUserAuthenticated() && (h(a, "Unauthorized", !1, c),
            CTXS.Events.publish(CTXS.Events.resources.challenged)))
        }
        if (CTXS.ResourcesClient) {
            a.launchstatusurl || (a.launchstatusurl = a.launchurl.replace("/LaunchIca/", "/GetLaunchStatus/").replace("/Launch/", "/GetLaunchStatus/"));
            var e = CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER;
            CTXS.ResourcesClient.getLaunchStatus(a, e, k, f)
        }
    }</pre>

Specifically, this line under the switch statement:

CTXS.Device.isIE() && CTXS.ClientManager.usingNativeClientInInternetZone() ? (g(a, CTXS.LAUNCH\_SUCCESS), g(a, CTXS.LAUNCH\_READY), a.isLaunchReady = !0) : (CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER && (e.ticket = f.fileFetchTicket, e.staTicket = f.fileFetchStaTicket, e.fileFetchUrl = f.fileFetchUrl, e.serverProtocolVersion = f.serverProtocolVersion), d(a, c, e));

I found if I modified the UserAgent at that exact point so CTXS.Device.isIE() did not return TRUE that it would execute the GetLaunchStatus.

Now that I suspect I have my culprit I examined the code that calls GetLaunchStatus URL and added it to my custom.js file:

<pre class="lang:js decode:true ">if (CTXS.Device.isIE()) {
              app.launchstatusurl || (app.launchstatusurl = app.launchurl.replace("/LaunchIca/", "/GetLaunchStatus/").replace("/Launch/", "/GetLaunchStatus/"));
              var e = CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER;
              CTXS.ResourcesClient.getLaunchStatus(app, e)
       }
</pre>

The end result now looks like this:

<pre class="lang:default decode:true ">CTXS.Extensions.doLaunch =  function(app, action) {
	//check for Notepad and configure clientname
	if (app.name == "Notepad") {
		$.ajaxSetup({
			headers : {
				'NewClientName':'ThisIsATest'
			}
		});
	}
	//check for Internet Explorer and configure clientname
	if (app.name == "Internet Explorer 11 - Test") {
		console.log("Customizing Client Name");
		$.ajaxSetup({
			headers : {
				'NewClientName':'DifferentClientName'
			}
		});
	}

	if (CTXS.Device.isIE()) {
		app.launchstatusurl || (app.launchstatusurl = app.launchurl.replace("/LaunchIca/", "/GetLaunchStatus/").replace("/Launch/", "/GetLaunchStatus/"));
		var e = CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER;
		CTXS.ResourcesClient.getLaunchStatus(app, e)
	}

    action();
	delete $.ajaxSettings.headers["NewClientName"]; //Remove header
};
</pre>

And now my customizations are called correctly and work with IE11. Â I haven&#8217;t done extensive testing so &#8220;your mileage may vary&#8221; with this fix, but if you are finding your customizations aren&#8217;t operating correctly you may want to try this fix.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->