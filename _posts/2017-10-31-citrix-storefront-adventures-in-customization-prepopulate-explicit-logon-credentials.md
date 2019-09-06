---
id: 2552
title: Citrix Storefront - Adventures in customization - Prepopulate Explicit Logon Credentials
date: 2017-10-31T08:48:15-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2552
permalink: /2017/10/31/citrix-storefront-adventures-in-customization-prepopulate-explicit-logon-credentials/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2017/10/Prepopulate.mp4
    185
    video/mp4
    
image: /wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-11.05.28-PM.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
Citrix Storefront allows you to prepopulate the credentials for your Explicit Logon. Â The explicit logon screen is generally seen here:

<img class="aligncenter size-full wp-image-2553" src="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.38.16-PM.png" alt="" width="1132" height="706" srcset="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.38.16-PM.png 1132w, http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.38.16-PM-300x187.png 300w, http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.38.16-PM-768x479.png 768w" sizes="(max-width: 1132px) 100vw, 1132px" /> 

And you can prepopulate the Username/Password fields. Â If you don't want to prepopulate the password, that's fine too. Â There are 3 properties and none are required. Â Username, Password and Domain. Â In order to prepopulate you must pass your credentials through to Storefront somehow, either as a cookie, header or as a URL search query. Â I will demo it in the URL search query since I already have that code for pulling the parameters. Â You must have "Explicit Authentication" enabled, aka, "User name and Password":

<img class="aligncenter size-full wp-image-2554" src="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.52.53-PM.png" alt="" width="553" height="426" srcset="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.52.53-PM.png 553w, http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.52.53-PM-300x231.png 300w" sizes="(max-width: 553px) 100vw, 553px" /> 

Put the following code into your custom.js file:

<pre class="lang:js decode:true ">CTXS.getParametersFromQueryString(window.location.search.substring(1))  //grab URL query parameters
var domain = CTXS.getParametersFromQueryString(window.location.search.substring(1)).domain
var username = CTXS.getParametersFromQueryString(window.location.search.substring(1)).username
var password = CTXS.getParametersFromQueryString(window.location.search.substring(1)).password

CTXS.Extensions.beforeLogon = function(callback) {
	if (username) {
		$.ctxs.ctxsFormsAuthentication.prototype.options.prePopulatedCredentials = { domain: domain, username: username, password: password };
	}
	callback();
};
</pre>

The url to query is:

<pre class="lang:default decode:true ">http://storefront.bottheory.local/Citrix/StoreWeb/?domain=BOTTHEORY&username=ttye&password=SometimesIW2ntAMemorablePassword</pre>

And the result:

<div style="width: 1128px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2552-33" width="1128" height="706" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/10/Prepopulate.mp4?_=33" /><a href="http://theorypc.ca/wp-content/uploads/2017/10/Prepopulate.mp4">http://theorypc.ca/wp-content/uploads/2017/10/Prepopulate.mp4</a></video>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->