---
id: 1803
title: Citrix Netscaler 11.1 Unified Gateway and a non-working Citrix HTML5 Receiver
date: 2016-12-06T23:02:52-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1803
permalink: /2016/12/06/citrix-netscaler-11-1-unified-gateway-and-a-non-working-citrix-html5-receiver/
image: /wp-content/uploads/2016/12/storefront2.png
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - XenApp
  - XenDesktop
---
We setup a Citrix Unified Gateway for a proof of concept and were having an issue getting the HTML5 Receiver to connect for external connections. Â It was presenting an error message: &#8220;Citrix Receiver cannot connect to the server&#8221;. Â We [followed this documentation](https://www.citrix.com/blogs/2015/07/08/receiver-internals-how-receiver-for-html5-chrome-connections-work/). Â It states, for our use case:

> What would probably help is having a proxy that can parse out all websocket traffic and convert to ICA/CGP traffic without any need of changes to XA/XD. Netscaler Gateway does exactly this&#8230; &#8230;NetScaler Gateway also doesnâ€™t need any special configuration for listening over websockets&#8230;
> 
> **Connections via NetScaler Gateway:**
> 
> &#8230;When a gateway is involved, the connections are similar in all Receivers. Here, Gateway acts as <span style="text-decoration: underline;"><strong>WebSocket proxy</strong></span> and in-turn **opens ICA/CGP/SSL _<span style="text-decoration: underline;">native</span>_ socket connections** to backend XenApp and XenDesktop. &#8230;
> 
> &#8230;So using a NetScaler Gateway would help here for ease of deployment. <span style="text-decoration: underline;"><strong>Connection to gateway is SSL/TLS</strong></span> and **gateway to XenApp/XenDesktop is <span style="text-decoration: underline;">ICA/CGP</span>**&#8230;.

And [additional documentation here](https://docs.citrix.com/en-us/receiver/html5/2-2/configuring.html).

> <p class="p">
>   WebSocket connections are also <span style="text-decoration: underline;"><strong>disabled by default</strong></span> on NetScaler Gateway. For remote users accessing their desktops and applications through NetScaler Gateway, you must create an <span style="text-decoration: underline;"><strong>HTTP profile with WebSocket connections enabled</strong></span> and either bind this to the NetScaler Gateway virtual server or apply the profile globally. For more information about creating HTTP profiles, seeÂ <a class="xref" href="https://docs.citrix.com/en-us/netscaler/10-5/ns-system-wrapper-10-con/ns-http-con.html">HTTP Configurations</a>.
> </p>

<p class="p">
  Ok. Â So we did the following:
</p>

<li class="p">
  We enabled WebSocket connections on Netscaler via the HTTP Profiles
</li>
<li class="p">
  We configured <a href="http://www.carlstalhood.com/storefront-3-5-configuration-for-netscaler-gateway/">Storefront with HTML5 Receiver and configured it for talking to the Netscaler</a>.
</li>

And then we tried launching our application:

<img class="aligncenter size-full wp-image-1805" src="http://theorypc.ca/wp-content/uploads/2016/12/cannot_connect_to_server.jpg" alt="" width="471" height="262" srcset="http://theorypc.ca/wp-content/uploads/2016/12/cannot_connect_to_server.jpg 471w, http://theorypc.ca/wp-content/uploads/2016/12/cannot_connect_to_server-300x167.jpg 300w" sizes="(max-width: 471px) 100vw, 471px" /> 

We started our investigation. Â The first thing we did was test to see if HTML5 Receiver works at all. Â We configured and enabled websockets on our XenApp servers and then logged into the Storefront server directly, and internally. Â We were able to launch applications without issue.

The second thing we did was [enable logging for HTML5 receiver](https://docs.citrix.com/en-us/receiver/html5/2-0/user-experience.html):

<div class="par parsys">
  <div class="parbase base richtext section">
    <div class="show-bullets">
      <div class="section">
        <blockquote>
          <h2 class="title sectiontitle">
            To view Citrix Receiver for HTML5 logs
          </h2>
          
          <p class="p">
            To assist with troubleshooting issues, you can view Citrix Receiver for HTML5 logs generated during a session.
          </p>
          
          <ol class="ol">
            <li class="li">
              Log on to the Citrix Receiver for Web site.
            </li>
            <li class="li">
              In another browser tab or window, navigate toÂ <var class="keyword varname">siteurl</var>/Clients/HTML5Client/src/ViewLog.html, whereÂ <var class="keyword varname">siteurl</var>is the URL of the Citrix Receiver for Web site, typically http://<var class="keyword varname">server</var>.<var class="keyword varname">domain</var>/Citrix/StoreWeb.
            </li>
            <li class="li">
              On the logging page, clickÂ <span class="ph uicontrol">Start Logging</span>.
            </li>
            <li class="li">
              On the Citrix Receiver for Web site, access a desktop or application using Citrix Receiver for HTML5. <p class="p">
                The log file generated for the Citrix Receiver for HTML5 session is shown on the logging page. You can also download the log file for further analysis.
              </p>
            </li>
          </ol>
        </blockquote>
      </div>
    </div>
  </div>
</div>

This was the log file it generated:

<pre class="lang:default decode:true">start Session
SESSION:|:BROWSERINFO:|:navigator =Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.87 Safari/537.36
SESSION:|:BROWSERINFO:|:os =WINDOWS
SESSION:|:BROWSERINFO:|:browser =CHROME;version=54
SESSION:|:PREFERENCE:|:ajax=true
SESSION:|:CONNECTION:|:ICA:|:ica type=autoXhr
SESSION:|:CONNECTION:|:LOADTIME:|:ica load time =115
SESSION:|:CONNECTION:|:LOADTIME:|:script load =3102
SESSION:|:PREFERENCE:|:language=en
SESSION:|:CONNECTION:|:LOADTIME:|:language =3116
SESSION:|:CONNECTION:|:initializing session
SESSION:|:CONNECTION:|:UI:|:initializing ui-interface
SESSION:|:CONNECTION:|:LOADTIME:|:ui initialize =18
 SESSION:|:CONNECTION:|:ICA:|:ClientVersion 2.2.0.216
SESSION:|:CONNECTION:|:ICA:|:initializing ica-interface
SESSION:|:CONNECTION:|:LOADTIME:|:ica initialize =9
SESSION:|:ICA:|:Encryption level : Basic
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCPM 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXTW  
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXTWI 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCLIP
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCAM 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXMM  
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCTL 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXEUEM
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXFILE
INIT :|: CONNECTION :|: TRANSPORT DRIVER :|: TRYING FOR SOCKET CONNECTION ON netscalergateway.testurl.com : 443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: websocket-url=wss://netscalergateway.testurl.com:443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: Current Protocol Index is : 0
INIT :|: CONNECTION :|: TRANSPORT DRIVER :|: SOCKET CONNECTED. Setting CGP Channel
INIT :|: CONNECTION :|: CGP SOCKET :|: INFO :|: Start Initializing CGP Socket
INIT :|: CONNECTION :|: CGP SOCKET :|: INFO :|: Finish Initializing CGP SOCKET
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: websocket-url=wss://netscalergateway.testurl.com:443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: Current Protocol Index is : 1
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: websocket-url=wss://netscalergateway.testurl.com:443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: Current Protocol Index is : 2
SESSION:|:ICA:|:TRANSPORT:|:DRIVER:|:close with code=1006
</pre>

The &#8220;Close with code=1006&#8221; seemed to imply it was a &#8220;websocket&#8221; issue from google searches.

<img class="aligncenter wp-image-1804" src="http://theorypc.ca/wp-content/uploads/2016/12/1006.png" width="400" height="700" srcset="http://theorypc.ca/wp-content/uploads/2016/12/1006.png 853w, http://theorypc.ca/wp-content/uploads/2016/12/1006-172x300.png 172w, http://theorypc.ca/wp-content/uploads/2016/12/1006-768x1343.png 768w" sizes="(max-width: 400px) 100vw, 400px" /> 

&nbsp;

The last few events prior to the error are &#8220;websocket&#8221; doing&#8230; Â something.

I proceeded to spin up a home lab with XenApp and a Netscaler configured for HTML5 Receiver and tried connecting. Â It worked flawlessly via the Netscaler. Â I enabled logging and took another look:

<pre class="lang:default decode:true ">start Session
SESSION:|:BROWSERINFO:|:navigator =Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET4.0C; .NET4.0E; InfoPath.3; rv:11.0) like Gecko
SESSION:|:BROWSERINFO:|:os =WINDOWS
SESSION:|:BROWSERINFO:|:browser =MSIE;version=11
SESSION:|:PREFERENCE:|:ajax=true
SESSION:|:CONNECTION:|:ICA:|:ica type=autoOpener
SESSION:|:CONNECTION:|:LOADTIME:|:script load =301
SESSION:|:CONNECTION:|:LOADTIME:|:ica load time =341
SESSION:|:PREFERENCE:|:language=en
SESSION:|:CONNECTION:|:LOADTIME:|:language =342
SESSION:|:CONNECTION:|:initializing session
SESSION:|:CONNECTION:|:UI:|:initializing ui-interface
SESSION:|:CONNECTION:|:LOADTIME:|:ui initialize =28
 SESSION:|:CONNECTION:|:ICA:|:ClientVersion 2.2.0.216
SESSION:|:CONNECTION:|:ICA:|:initializing ica-interface
SESSION:|:SESSION MANAGER:|:START:|:command =1501
SESSION:|:SESSION MANAGER:|:END:|:command =1501
SESSION:|:SESSION MANAGER:|:START:|:command =1503
SESSION:|:SESSION MANAGER:|:WINDOW:|:SET_ENGINEICA
SESSION:|:SESSION MANAGER:|:END:|:command =1503
SESSION:|:SESSION MANAGER:|:START:|:command =1503
SESSION:|:SESSION MANAGER:|:WINDOW:|:SET_ENGINECTXCTL 
SESSION:|:SESSION MANAGER:|:END:|:command =1503
SESSION:|:SESSION MANAGER:|:START:|:command =1503
SESSION:|:SESSION MANAGER:|:WINDOW:|:SET_ENGINECTXCLIP
SESSION:|:SESSION MANAGER:|:END:|:command =1503
SESSION:|:SESSION MANAGER:|:START:|:command =1503
SESSION:|:SESSION MANAGER:|:WINDOW:|:SET_ENGINECTXGUSB
SESSION:|:SESSION MANAGER:|:END:|:command =1503
SESSION:|:SESSION MANAGER:|:START:|:command =319
SESSION:|:SESSION MANAGER:|:WRAPPER:|:process command
SESSION:|:SESSION MANAGER:|:START:|:command =1502
SESSION:|:SESSION MANAGER:|:END:|:command =1502
SESSION:|:SESSION MANAGER:|:START:|:command =0
SESSION:|:SESSION MANAGER:|:WINDOW:|:SESSION_LAUNCH_APPLICATION
SESSION:|:ICA:|:Encryption level : Basic
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCPM 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXTW  
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXTWI 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCLIP
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXMM  
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXCTL 
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXEUEM
SESSION:|:ICA:|:CHANNEL:|:supported channelCTXFILE
INIT :|: CONNECTION :|: TRANSPORT DRIVER :|: TRYING FOR SOCKET CONNECTION ON netscaler.bottheory.local : 443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: websocket-url=wss://netscaler.bottheory.local:443
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: Current Protocol Index is : 0
INIT :|: CONNECTION :|: TRANSPORT DRIVER :|: SOCKET CONNECTED. Setting CGP Channel
INIT :|: CONNECTION :|: CGP SOCKET :|: INFO :|: Start Initializing CGP Socket
INIT :|: CONNECTION :|: CGP SOCKET :|: INFO :|: Finish Initializing CGP SOCKET
SESSION:|:SESSION MANAGER:|:NONSEAMLESS:|:ica connection with new connection
SESSION:|:SESSION MANAGER:|:END:|:command =0
SESSION:|:CONNECTION:|:LOADTIME:|:ica initialize =42
SESSION:|:CONNECTION:|:initializing session
SESSION:|:CONNECTION:|:LOADTIME:|:ui initialize =2
 SESSION:|:CONNECTION:|:ICA:|:ClientVersion 2.2.0.216
SESSION:|:CONNECTION:|:LOADTIME:|:ica initialize =3
INIT :|: CONNECTION :|: TRANSPORT:|: WEBSOCKET :|: connected
INIT :|: CONNECTION :|: WEB SOCKET :|: INFO :|: WEB SOCKET IS READY TO SEND/RECEIVE DATA.
SESSION :|: CGP :|: STATE :|: CGP-CORE :|: Changing core state from :0 To 1
SESSION :|: CGP :|: ReliabilityParamsCapability :|: INFO:|:Adding Reliability Params Capability to Bind Request.
SESSION :|: CGP :|: CGP SOCKET :|: INFO :|: TCP Handshake Sent!!!!
SESSION :|: CGP :|: CGP-CORE :|: Hit the State Signature
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Start process Signature
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Processing Bind response
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of service ID: 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of capability ID: 6
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Security Ticket Capability Handling hit
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of service ID: 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of capability ID: 5
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Session Reliability Capability Handling hit
 SESSION :|: CGP :|: CGP-CORE :|: INFO:|: RECONNECT FLAG is 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of service ID: 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of capability ID: 9
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Reliability Params Capability Handling hit
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: The server sent values of  RELIABILITY TIMEOUT : 180 UI FLAGS : 1 UI DIMMING PERCENTAGE : 80 TCP TIMEOUT: 60
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of service ID: 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of capability ID: 7
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Keep Alives Capability Handling hit
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of service ID: 0
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Setting Capability of capability ID: 1
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Bind Capability Handling hit
SESSION :|: CGP :|: STATE :|: CGP-CORE :|: Changing core state from :1 To 2
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: Session Connected after Bind Response
SESSION :|: CGP :|: CGP-CORE :|: INFO:|: End of Processing Bind Response data
SESSION :|: CGP :|: CGP SOCKET :|: INFO :|: CGP HANDSHAKE SUCCESSFUL. Sending CHANNEL OPEN REQUEST
SESSION :|: CGP :|: CGP SOCKET :|: INFO :|: TCP Handshake Complete!!!!
SESSION:|:ICA:|:WINSTATION:|:DRIVER:|:init request
SESSION:|:ICA:|:TIMEZONE:|Windows OS EN returning timezone Mountain Standard Time
SESSION:|:ICA:|:TIMEZONE:|Windows OS EN returning timezone Mountain Standard Time
SESSION:|:ICA:|:TIMEZONE:|:time zone name: Mountain Standard Time bias 420 dstOffset 60
SESSION:|:ICA:|:TIMEZONE:|Windows OS EN returning timezone Mountain Standard Time
â€¦</pre>

So there is a lot of differences but we focus on the point of failure in our enterprise netscaler we see it seems to retryÂ or try different indexes (3 in total, 0, 1 and 2).

So there is a lot of evidence that websockets seem to be culprit. Â We have tried removing Netscaler from the connection picture by connecting directly to Storefront and HTML5 receiver works. Â We have configured both Netscaler and Storefront (with what we think) is a correct configuration. Â And still we are getting a failure.

I opened up a call to Citrix.

It was a fairly frustrating experience. Â I had tech&#8217;s ask me to go to &#8220;Program Files\Citrix\Reciever&#8221; and get the receiver version (hint, hint, this does not exist with HTML5). Â I captured packets of the failure &#8220;in motion&#8221; and they told me, &#8220;it&#8217;s not connecting to your XenApp server&#8221;. Â &#8212; Yup. Â That&#8217;s the Problem.

It seems that HTML5 is either so new (it&#8217;s not now), so simple (it&#8217;s not really), or tech&#8217;s are just poorly trained. Â I reiterated to them &#8220;why does it make 3 websocket connections on the &#8216;bad&#8217; netscaler? Why does the &#8216;good&#8217; netscaler appear to connect the first time without issue?&#8221; Â I felt the tech&#8217;s ignore andÂ beat around the bush regarding websockets and more focus put on the &#8220;Storefront console&#8221;. Â Storefront itself was NOT logging ANYTHING to the event logs. Â Apparently this is weird for a storefrontÂ failure. Â I suspected Storefront was operating correctly and I was getting frustrated we weren&#8217;t focusing on what I suspected was the problem (websockets). Â So I put the case on hold so I could focus on doing the troubleshooting myself instead of going around in circles on setting HTML5 to &#8220;always use&#8221; or &#8220;use only when native reciever is not detected&#8221;.

Reviewing the documentation for the umpteenth time this &#8220;troubleshooting connections&#8221; tidbit came out:

> **Troubleshooting Connections:**
> 
> In cases where you are not able to connect some of the following points might help in finding out the problem. They also can be used while opening support case or seeking help in forums:
> 
> 1) Logging: Basic connection related logs are logged byÂ [Receiver for HTML5](http://docs.citrix.com/en-us/receiver/html5/1-7/receiver-html5-ux.html)Â andÂ [Receiver for Chrome](http://docs.citrix.com/en-us/receiver/html5/1-7/receiver-html5-ux.html).
> 
> 2) <span style="text-decoration: underline;"><strong>Browser console logs: Browsers would show errors related to certificates or network related failures here</strong></span>.

  * > Receiver for HTML5: Open developer tools for HDX session browser tab.Â _Tip: In Windows, use F12 shortcut in address bar of session page_.

  * > Receiver for Chrome: Go to chrome://inspect, click on Apps on left side. Click on inspect for Citrix Receiver pages (Main.html, SessionWindow.html etc)

The browser may show a log? Â I wish I would have thought of that earlier. Â And I wish Citrix would have put that in the actual &#8220;Receiver for HTML5&#8221; documentation as opposed to buried in a blog article.

So I opened the Console in Chrome, launched my application and reviewed the results:

<img class="aligncenter size-full wp-image-1807" src="http://theorypc.ca/wp-content/uploads/2016/12/chrome-troubleshooting.png" alt="" width="1435" height="954" srcset="http://theorypc.ca/wp-content/uploads/2016/12/chrome-troubleshooting.png 1435w, http://theorypc.ca/wp-content/uploads/2016/12/chrome-troubleshooting-300x199.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/chrome-troubleshooting-768x511.png 768w" sizes="(max-width: 1435px) 100vw, 1435px" /> 

We finally have some human readable information.

Websocket connections are failing during the handshake &#8220;Unexpected response code: 302&#8221;

What heck does 302 mean? Â I installed Fiddler and did another launch withe Fiddler tracing:

<img class="aligncenter size-full wp-image-1808" src="http://theorypc.ca/wp-content/uploads/2016/12/broken_myapps_websocket_fiddler.png" alt="" width="1039" height="791" srcset="http://theorypc.ca/wp-content/uploads/2016/12/broken_myapps_websocket_fiddler.png 1039w, http://theorypc.ca/wp-content/uploads/2016/12/broken_myapps_websocket_fiddler-300x228.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/broken_myapps_websocket_fiddler-768x585.png 768w" sizes="(max-width: 1039px) 100vw, 1039px" /> 

&nbsp;

I highlighted the area where it tells us it&#8217;s attempting to connect with websockets. Â We can see in the response packet we are getting redirected, that&#8217;s what &#8216;302&#8217; means. Â I then found a website that lets you test your server to ensure websockets are working. Â I tried it on our &#8216;bad&#8217; netscaler:

<img class="aligncenter size-full wp-image-1812" src="http://theorypc.ca/wp-content/uploads/2016/12/WebSocket._failureorg.png" alt="" width="780" height="680" srcset="http://theorypc.ca/wp-content/uploads/2016/12/WebSocket._failureorg.png 780w, http://theorypc.ca/wp-content/uploads/2016/12/WebSocket._failureorg-300x262.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/WebSocket._failureorg-768x670.png 768w" sizes="(max-width: 780px) 100vw, 780px" /> 

&nbsp;

Hitting &#8216;Connect&#8217; left nothing in the log. Â However, when I tried it with my &#8216;good&#8217; netscaler&#8230;

<img class="aligncenter size-full wp-image-1813" src="http://theorypc.ca/wp-content/uploads/2016/12/WebSocket_wokrk.org_.png" alt="" width="780" height="680" srcset="http://theorypc.ca/wp-content/uploads/2016/12/WebSocket_wokrk.org_.png 780w, http://theorypc.ca/wp-content/uploads/2016/12/WebSocket_wokrk.org_-300x262.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/WebSocket_wokrk.org_-768x670.png 768w" sizes="(max-width: 780px) 100vw, 780px" /> 

&nbsp;

It works! Â So we can test websockets without having to launch and close the application over and over&#8230;

&nbsp;

So we started to investigate the Netscaler. Â We found numerous policies that did URL or content redirection that would be taking place with the packet formulated like so. Â We then compared our Netscaler to the one in my homelab and did find one, subtle difference:

<img class="aligncenter size-full wp-image-1811" src="http://theorypc.ca/wp-content/uploads/2016/12/broken_netscaler.png" alt="" width="1416" height="303" srcset="http://theorypc.ca/wp-content/uploads/2016/12/broken_netscaler.png 1416w, http://theorypc.ca/wp-content/uploads/2016/12/broken_netscaler-300x64.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/broken_netscaler-768x164.png 768w" sizes="(max-width: 1416px) 100vw, 1416px" /> 

The one on the left is showing a rule for HTTP.REQ.URL.CONTAINS\_ANY(&#8220;aaa\_path&#8221;) where the one on the right just shows &#8220;is\_vpn\_url&#8221;. Â Investigating further it was stated that our team was trying to get AAA authentication working and this was an option set during a troubleshooting stage. Â Apparently, it was forgotten or overlooked when the issue was resolved (it was not applicable and can be removed). Â So we set it back to having the &#8220;is\_vpn\_url&#8221; and retried&#8230;

It worked! Â I tried the &#8216;websockets.org&#8217; test and it connected now! Â Looking in the Chrome console showed:

<img class="aligncenter size-full wp-image-1814" src="http://theorypc.ca/wp-content/uploads/2016/12/chrome_working_console.png" alt="" width="1434" height="950" srcset="http://theorypc.ca/wp-content/uploads/2016/12/chrome_working_console.png 1434w, http://theorypc.ca/wp-content/uploads/2016/12/chrome_working_console-300x199.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/chrome_working_console-768x509.png 768w" sizes="(max-width: 1434px) 100vw, 1434px" /> 

&nbsp;

Success! Â It doesn&#8217;t pause on the websocket connection and the console logging shows some interesting information. Â Fiddler, with the working connection, now displayed the following:

<img class="aligncenter size-full wp-image-1815" src="http://theorypc.ca/wp-content/uploads/2016/12/fiddler_working_websockets-1.png" alt="" width="1114" height="788" srcset="http://theorypc.ca/wp-content/uploads/2016/12/fiddler_working_websockets-1.png 1114w, http://theorypc.ca/wp-content/uploads/2016/12/fiddler_working_websockets-1-300x212.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/fiddler_working_websockets-1-768x543.png 768w" sizes="(max-width: 1114px) 100vw, 1114px" /> 

Look! Â A handshark response!

&nbsp;

So, to review what we learned:

  1. Connections via Netscaler to HTML5 reciever do NOT <span style="text-decoration: underline;">require</span>Â (but is possible) aÂ SSL connection on each target XenApp device
  2. Connection via Netscaler work over standard port (2598/1494) and do not require any special configuration on your XenApp server.
  3. You can use &#8216;http://www.websocket.org/echo.html&#8217; to test your Netscaler to ensure websockets are open and working.
  4. Fiddler can tell you verbose information on yourÂ websocket connection and their contents.
  5. The web browser&#8217;s Javascript console is perfect to look at verbose messages in HTML5.

&nbsp;

And with that, we are working and happy, Good Night!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->