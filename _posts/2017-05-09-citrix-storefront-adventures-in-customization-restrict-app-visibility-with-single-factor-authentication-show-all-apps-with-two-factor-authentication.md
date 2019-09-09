---
id: 2239
title: Citrix Storefront - Adventures in customization - Restrict app visibility with single factor authentication, show all apps with two-factor authentication
date: 2017-05-09T22:16:33-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2239
permalink: /2017/05/09/citrix-storefront-adventures-in-customization-restrict-app-visibility-with-single-factor-authentication-show-all-apps-with-two-factor-authentication/
image: /wp-content/uploads/2017/05/app_icon.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
I have been working with a colleague of mine (Saman Salehian) who has been working on a project with a Citrix Netscaler.  One of the hopes of this project is to offer Citrix applications externally.  A problem was posed to me about restricting users to only access non-critical, non-patient facing applications (eg, Outlook, intranet site, etc.) if they logged in with their domain credentials, but if users were using a two factor authentication method to show all applications.

Citrix has 3 articles ([one](https://www.citrix.com/blogs/2014/03/27/hiding-applications-in-citrix-storefront/), [two](https://support.citrix.com/article/CTX204869) [three](https://www.citrix.com/blogs/2014/05/20/now-you-see-me-now-you-dont-a-guide-to-hiding-published-resources/)) that I've been able to find about executing on this.  The problem with these articles is that they are now <span style="text-decoration: underline;"><strong>outdated</strong></span>.  Citrix has a much more flexible and (In My Humble Opinion) better way to hide/show applications.  And that is through the [Receiver Extension APIs](https://docs.citrix.com/en-us/storefront/3/migrate-wi-to-storefront/receiver-extension-apis.html).  Through a single store, I'll be able to show and hide applications _**dynamically**_.

The two API calls that are relevant are:

<p style="padding-left: 30px;">
  <strong>excludeApp(app)</strong><br /> Exclude an application completely from all UI, even if it would normally be included.
</p>

<p style="padding-left: 30px;">
  <strong>includeApp(app)</strong><br /> Include an application, even if it would normally be excluded. For example a platform might exclude applications intended for a different platform.
</p>

The architecture of this solution looks like this:

<img class="aligncenter size-large wp-image-2241" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-09-at-10.01.30-PM-1600x742.png" alt="" width="1140" height="529" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-09-at-10.01.30-PM-1600x742.png 1600w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-09-at-10.01.30-PM-300x139.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-09-at-10.01.30-PM-768x356.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

It's pretty damn simple.  Look that a specific cookie has a specific value and if it does <span style="text-decoration: underline;"><strong>NOT</strong></span> have that value, exclude the app(s) from being shown.

So the role of the Netscaler here is when the user logs on, it will write a cookie based on the authentication.  Our Storefront script will check for the value of that cookie.  If the cookie contains our known value then we iterate through all applications and look for some unique text we've set in the application description field (this works with both & 6.5 and 7.X) and hide those applications.  For my example, I've added " 2FA" to the application description field for the applications I want excluded from single-factor authentication. Note: I've required a 'space' before the characters 2FA.

<pre class="lang:js decode:true ">//get cookies function
function getCookie(name) {
    var results = document.cookie.match('(^|;) ?' + name + '=([^;]*)');
    return results ? unescape(results[2]) : null;
}

var logonmethod = getCookie("logonmethod");

if (logonmethod == "1FA") {
	CTXS.Extensions.excludeApp = function(app) {
		//do a javascript search for our text.  
		//if the text is found then the value of €˜findme€™ will be > 1. If it€™s not found then it will be -1.
		var findme = app.description.search(" 2FA");
		if (findme != -1) {
			CTXS.trace("hiding app from 1FA:" + app.description);
			return true;
		}
	};
}
</pre>

And that's it!  A deliciously simple addition to **\custom\script.js**.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->