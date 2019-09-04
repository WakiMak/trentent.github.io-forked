---
id: 2637
title: 'Citrix Storefront â€“ Adventures in customization â€“ Default to "Store" view if you have no favourited app's'
date: 2017-12-22T12:33:06-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2637
permalink: /2017/12/22/citrix-storefront-adventures-in-customization-default-to-store-view-if-you-have-no-favourited-apps/
image: /wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-12.34.11-PM.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
We are in the process of migrating users from Web Interface to Storefront. Â We have identified a potential issue; new users are directed to the "Favourites" view which doesn't have any applications be default, instead it has instructions on how to add apps to the favourites view.

<div id="attachment_2638" style="width: 729px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2638" class="wp-image-2638 size-full" src="http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-12.18.42-PM.png" alt="" width="719" height="401" srcset="http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-12.18.42-PM.png 719w, http://theorypc.ca/wp-content/uploads/2017/12/Screen-Shot-2017-12-22-at-12.18.42-PM-300x167.png 300w" sizes="(max-width: 719px) 100vw, 719px" /></p> 
  
  <p id="caption-attachment-2638" class="wp-caption-text">
    New users might say, "Where did my apps go?!"
  </p>
</div>

The concern is users may become confused because Web Interface shows all your applications, and this new view shows none. Â What we want to do to solve this is default to the "Store" view if you have no favourite apps, and default to the favourites view if you have at least 1 app favourite.

&nbsp;

We can do this.

&nbsp;

<pre class="lang:js decode:true ">CTXS.Extensions.afterDisplayHomeScreen = function (callback) {
	/* If the user has no favorited apps, set the view to the category view */
	if (CTXS.Store.getMyApps().length == 0) {
       	        CTXS.ExtensionAPI.changeView("store")
	}
};</pre>

Just add the code above to your custom.js file and the default view will be changed to the store if you have no favorited apps. Â Done!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->