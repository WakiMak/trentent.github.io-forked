---
id: 2544
title: Citrix Storefront â€“ Adventures in customization â€“ Login via credentials in URL search query
date: 2017-10-30T08:34:31-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2544
permalink: /2017/10/30/citrix-storefront-adventures-in-customization-login-via-credentials-in-url-search-query/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2017/10/StoreFrontURL.mp4
    187
    video/mp4
    
image: /wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.34.06-PM.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
If you use a 3rd party service to connect to your Citrix Storefront environment, you may want to &#8220;pass-through&#8221; credentials without using domain authentication or whatever. Â This post illustrates how you can login to your Storefront environment using nothing more than a URL with your credentials embedded in them. Â To enable this functionality, this code must be in your custom.js file.

<pre class="lang:js decode:true ">// CTXS.getParametersFromQueryString(window.location.search.substring(1))  //grab URL query parameters
var username = CTXS.getParametersFromQueryString(window.location.search.substring(1)).username
var password = CTXS.getParametersFromQueryString(window.location.search.substring(1)).password


CTXS.Extensions.beforeLogon = function(callback) {
	if (username) {
		var a = "PostCredentialsAuth/Login";
		$.ctxsAjax({

	                type: "POST",
        	        url: a,
					async: false,
                	data: { username: username, password: password },
                	dataType: "json",

                	suppressEvents: !0,
                	refreshSession: !0

            	})
		CTXS.Location.assign(CTXS.Location.origin + CTXS.Location.pathname);  //remove URL query parameters
      	}
	callback();
};
</pre>

You MUST have HTTP Basic enabled as an authentication method on your Citrix Storefront Store.

<img class="aligncenter size-full wp-image-2545" src="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.22.38-PM.png" alt="" width="1111" height="638" srcset="http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.22.38-PM.png 1111w, http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.22.38-PM-300x172.png 300w, http://theorypc.ca/wp-content/uploads/2017/10/Screen-Shot-2017-10-29-at-10.22.38-PM-768x441.png 768w" sizes="(max-width: 1111px) 100vw, 1111px" /> 

The URL to login would look like this:

<pre class="lang:default decode:true">http://storefront.bottheory.local/Citrix/StoreWeb/?username=BOTTHEORY\ttye&password=C0mplexPAsswordHorseStaple</pre>

Put it all together:

<div style="width: 1128px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2544-32" width="1128" height="708" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/10/StoreFrontURL.mp4?_=32" /><a href="http://theorypc.ca/wp-content/uploads/2017/10/StoreFrontURL.mp4">http://theorypc.ca/wp-content/uploads/2017/10/StoreFrontURL.mp4</a></video>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->