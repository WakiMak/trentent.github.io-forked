---
id: 639
title: 'How to enable "Adaptive Display" in & 6.5'
date: 2013-06-11T16:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/06/11/how-to-enable-adaptive-display-in-&-6-5/
permalink: /2013/06/11/how-to-enable-adaptive-display-in-&-6-5/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/06/how-to-enable-adaptive-display-in.html
blogger_internal:
  - /feeds/7977773/posts/default/5957315234365687220
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Group Policy
  - Performance
  - &
---
Contrary to the documentation in the Group Policy settings for Citrix, & requires the following settings configured for Adaptive Display to be enabled:

User settings  
**Minimum Image Quality**  
This setting specifies the minimum acceptable image quality for Adaptive Display. The less compression used, the higher the quality of images displayed. Choose from Ultra High, Very High, High, Normal, or Low compression.  
By default, this is set to Normal.

**Moving Image Compression**  
This setting specifies whether or not Adaptive Display is enabled. Adaptive Display automatically adjusts the image quality of videos and transitional slides in slide shows based on available bandwidth. With Adaptive Display enabled, users should see smooth-running presentations with no reduction in quality.  
By default, this is set to Enabled.

**Target Minimum Frame Rate**  
This setting specifies the minimum frame rate you want. The minimum is a target and is not guaranteed. Adaptive Display automatically adjusts to stay at or above this setting where possible.  
By default, this is set to 10 frames per second.

**Progressive Compression Level**  
Set to Disabled

Even though the GPO's state these only apply to XenDesktop, they also apply to & and can be confirmed if you publish HDX Monitor 3.0 on a & server and monitor the ICA session, you can see the transient quality increasing or decreasing depending on your scenario.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->