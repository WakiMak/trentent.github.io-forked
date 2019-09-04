---
id: 1481
title: 'EventID 6003 &#8211; The winlogon notification subscriber <TrustedInstaller> was unavailable to handle a critical notification event.'
date: 2016-05-31T11:59:35-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1481
permalink: /2016/05/31/eventid-6003-the-winlogon-notification-subscriber-was-unavailable-to-handle-a-critical-notification-event/
categories:
  - Blog
  - Uncategorized
tags:
  - Internet Explorer
  - Performance
---
We updated one of our Citrix XenApp servers and this message started flooding our Application event log:  
&#8220;The winlogon notification subscriber <TrustedInstaller> was unavailable to handle a critical notification event.&#8221;

So what&#8217;s going on here?

Examining the registry on a &#8216;good&#8217; working system and the &#8216;bad&#8217; system revealed the following:

<div id="attachment_1483" style="width: 878px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1483" class="wp-image-1483 size-full" src="http://theorypc.ca/wp-content/uploads/2016/05/2-1.png" alt="2" width="868" height="410" srcset="http://theorypc.ca/wp-content/uploads/2016/05/2-1.png 868w, http://theorypc.ca/wp-content/uploads/2016/05/2-1-300x142.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/2-1-768x363.png 768w" sizes="(max-width: 868px) 100vw, 868px" /></p> 
  
  <p id="caption-attachment-1483" class="wp-caption-text">
    Good TrustedInstaller &#8211; No Error 6003
  </p>
</div>

&nbsp;

<div id="attachment_1482" style="width: 981px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1482" class="wp-image-1482 size-full" src="http://theorypc.ca/wp-content/uploads/2016/05/1-1.png" alt="1" width="971" height="348" srcset="http://theorypc.ca/wp-content/uploads/2016/05/1-1.png 971w, http://theorypc.ca/wp-content/uploads/2016/05/1-1-300x108.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/1-1-768x275.png 768w" sizes="(max-width: 971px) 100vw, 971px" /></p> 
  
  <p id="caption-attachment-1482" class="wp-caption-text">
    Bad TrustedInstaller &#8211; Error 6003
  </p>
</div>

How did that value get there?

It turns out we installed Internet Explorer 11 with our patch cycle &#8212; but that in and of itself did not cause our issue. Â Additional components for IE 11 were installed as well:  
<img class="aligncenter size-full wp-image-1484" src="http://theorypc.ca/wp-content/uploads/2016/05/3-1.png" alt="3" width="771" height="123" srcset="http://theorypc.ca/wp-content/uploads/2016/05/3-1.png 771w, http://theorypc.ca/wp-content/uploads/2016/05/3-1-300x48.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/3-1-768x123.png 768w" sizes="(max-width: 771px) 100vw, 771px" /> 

&#8220;Microsoft Windows English Spelling Package&#8221; and &#8220;Microsoft Windows English Hyphenation Package&#8221;

Neither of these packages are present on any of the &#8216;working&#8217; systems. Â I tested to determine which of them placed the registry key there&#8230;

It turns out both of them do. Â If you uninstall \*either\* package it will remove the &#8216;CreateSession&#8217; value. Â Since these packages are not in our standard build we are removing both.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->