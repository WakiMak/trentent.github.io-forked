---
id: 1099
title: 'How to enable &#8220;Adaptive Display&#8221; in XenApp 6.5'
date: 2016-04-25T08:01:02-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/639-revision-v1/
permalink: /2016/04/25/639-revision-v1/
---
Contrary to the documentation in the Group Policy settings for Citrix, XenApp requires the following settings configured for Adaptive Display to be enabled:

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

Even though the GPO&#8217;s state these only apply to XenDesktop, they also apply to XenApp and can be confirmed if you publish HDX Monitor 3.0 on a XenApp server and monitor the ICA session, you can see the transient quality increasing or decreasing depending on your scenario.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->