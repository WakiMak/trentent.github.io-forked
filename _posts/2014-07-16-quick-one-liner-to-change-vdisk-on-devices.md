---
id: 602
title: Quick one-liner to change vDisk on devices
date: 2014-07-16T10:05:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/07/16/quick-one-liner-to-change-vdisk-on-devices/
permalink: /2014/07/16/quick-one-liner-to-change-vdisk-on-devices/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/07/quick-one-liner-to-change-vdisk-on.html
blogger_internal:
  - /feeds/7977773/posts/default/2255537400472621383
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - scripting
---
<pre class="lang:batch decode:true ">FOR /L %A IN (28,1,60) DO (
MCLI run assigndisklocator -p disklocatorname=&65Pn03 sitename=AHI storename=& devicename=WSCTXAPP4%A
MCLI run removedisklocator -p disklocatorname=&65Pn01 sitename=AHI storename=& devicename=WSCTXAPP4%A
)</pre>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->