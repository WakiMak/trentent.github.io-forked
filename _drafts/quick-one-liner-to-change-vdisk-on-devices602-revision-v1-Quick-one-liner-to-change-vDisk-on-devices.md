---
id: 1054
title: Quick one-liner to change vDisk on devices
date: 2016-04-24T22:33:56-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/602-revision-v1/
permalink: /2016/04/24/602-revision-v1/
---
<pre class="lang:batch decode:true ">FOR /L %A IN (28,1,60) DO (
MCLI run assigndisklocator -p disklocatorname=XenApp65Pn03 sitename=AHI storename=XenApp devicename=WSCTXAPP4%A
MCLI run removedisklocator -p disklocatorname=XenApp65Pn01 sitename=AHI storename=XenApp devicename=WSCTXAPP4%A
)</pre>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->