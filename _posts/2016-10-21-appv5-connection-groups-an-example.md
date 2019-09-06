---
id: 535
title: AppV5 - Connection groups, an example
date: 2016-10-21T12:50:13-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/2016/10/21/appv5-connection-groups-an-example/
permalink: /2016/10/21/appv5-connection-groups-an-example/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_internal:
  - /feeds/7977773/posts/default/2120186219282658173
image: /wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-09-2Bat-2B1.47.50-2BPM-1.png
categories:
  - Blog
tags:
  - AppV
---
Connections groups in AppV5 is a feature that allows different AppV packages to have visibility to each other by overlaying them overtop of each other. Â Why would you want to do this? Â In this real world example, we utilize connection groups for 2 AppV packages. Â One of these packages is updated bi-yearly or yearly and one of the packages is updated quarterly. Â If we created one monolith package then full revalidation testing needs to be completed each time it's updated. Â This process can take several days. Â By breaking it out into component packages we can ensure that only a subset of the package is changed and testing can be reduced to just that area which can usually be accomplished in a few hours.

The packages:  
[Raiser's Edge](https://www.blackbaud.com/fundraising-and-relationship-management/raisers-edge)  
[Address Accelerator](https://www.blackbaud.co.uk/notforprofit/direct-marketing/products/address-accelerator)

<div style="clear: both; text-align: center;">
</div>

To get started, we create our AppV packages:

Raiser's Edge

<div style="clear: both; text-align: center;">
</div>

Address Accelerator

<div style="clear: both; text-align: center;">
</div>

With our packages complete, we go onto the AppV Management Server and add the packages to the server:

[<img src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-09-2Bat-2B1.47.50-2BPM-1-300x64.png" width="320" height="68" border="0" />](http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-09-2Bat-2B1.47.50-2BPM-1.png)

Then we go to the Connection Groups and 'ADD CONNECTION GROUP'

<div style="clear: both; text-align: center;">
</div>

We publish the connection group to our targetted servers and both AppV packages 'merged' when you open either package. Â Now, we can update the 'Address Accelerator' portion on the quarterly basis that is required and simply add the new AppV version to the Address Accelerator package in the management console and the next publishing refresh it will be received on the server and the next user launch of the package will have the updated components. Â Smooth, easy and now we don't have to touch the actual program or its guts.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->