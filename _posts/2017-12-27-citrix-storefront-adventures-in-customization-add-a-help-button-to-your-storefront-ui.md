---
id: 2641
title: Citrix Storefront - Adventures in customization - Add a help button to your Storefront UI
date: 2017-12-27T07:00:27-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2641
permalink: /2017/12/27/citrix-storefront-adventures-in-customization-add-a-help-button-to-your-storefront-ui/
image: /wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-1.03.47-PM.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
<img class="aligncenter size-full wp-image-2642" src="http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-1.01.53-PM.png" alt="" width="864" height="393" srcset="http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-1.01.53-PM.png 864w, http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-1.01.53-PM-300x136.png 300w, http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-1.01.53-PM-768x349.png 768w" sizes="(max-width: 864px) 100vw, 864px" />

This customization is pretty easy. Â Add the following to your custom.js file:

<pre class="lang:default decode:true ">CTXS.ExtensionAPI.addHelpButton(
	function onClick() {
	CTXS.ExtensionAPI.openUrl("http://www.google.ca");
	}
);</pre>

Replace "http://www.google.ca" with the URL you want your help screen to be.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->