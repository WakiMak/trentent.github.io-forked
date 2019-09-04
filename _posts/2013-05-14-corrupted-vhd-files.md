---
id: 646
title: Corrupted VHD files
date: 2013-05-14T15:36:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/14/corrupted-vhd-files/
permalink: /2013/05/14/corrupted-vhd-files/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/corrupted-vhd-files.html
blogger_internal:
  - /feeds/7977773/posts/default/2439617557176731688
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - Provisioning Services
  - Target Device
---
Corrupt VHD files... Â We utilize PVS and when we were attempting to update the target device software utilizing Hyper-V we got several error messages saying the VHD is corrupted. Â The actual root of the issue is that PVS makes 16MB block size VHD files (thanks SAMAN!) and Windows 2008 only reads VHD files that are 512KB or 2MB block sized files.

Attempting to mount the VHD file via disk management fails as well. Â The only utility that would mount the VHD file that was generating the error messages at the end of this post is the Citrix CVHDMount utility available via PVS (C:\Program Files\Citrix\Provisioning Services\CVHDMount.exe)

Since our Hyper-V server was separateÂ from the PVS server, we needed to install the drivers to allow CVHDMount.exe to work. Â In order to do this you need to install the drivers in this folder:  
C:\Program Files\Citrix\Provisioning Services\drivers

You can right-click "Install" on theÂ cfsdep2.inf file.

For the other driver you need to open Device Manager, go through "Add new hardware...", "Add legacy hardware" and then browse to the drivers folder and add theÂ cvhdbusp6.inf.

Now we would mount the disk as a drive letter using this command line:

<pre class="lang:default decode:true ">C:\Users\trententtye>"C:\Program Files\Citrix\Provisioning Services\CVhdMount.exe" -p 1 "\\wsctxapp824\d$\&65Pn02.2.vhd"
Opening \\?\root#scsiadapter#0000#{c97efdbd-972c-4fe8-8279-9ce6c19fa260}
Bus interface opened.
Plugin device with SerialNumber: 1
filename: \Global??\UNC\wsctxapp824\d$\&65Pn02.2.vhd</pre>

At this point you need to set the disk to "Offline" in Disk Management.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-OL0ODpKMak0/UZKbk42oYWI/AAAAAAAAAOY/_mF1XAMRZnM/s1600/Capture.PNG"><img src="http://2.bp.blogspot.com/-OL0ODpKMak0/UZKbk42oYWI/AAAAAAAAAOY/_mF1XAMRZnM/s320/Capture.PNG" width="320" height="237" border="0" /></a>
</div>

From here we can now add the disk as a physical disk to Hyper-V.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-z_wHukiSC74/UZKuNRcAivI/AAAAAAAAAOo/_wexwaeNEeA/s1600/Capture2.PNG"><img src="http://3.bp.blogspot.com/-z_wHukiSC74/UZKuNRcAivI/AAAAAAAAAOo/_wexwaeNEeA/s320/Capture2.PNG" width="320" height="299" border="0" /></a>
</div>

And we can now do the Target Device Update. Â Error messages seen during this troubleshooting:

<pre class="lang:default decode:true ">"---------------------------
Virtual Disk Manager
---------------------------
The file or directory is corrupted and unreadable.Â 
---------------------------
OK Â Â 
---------------------------

"

"[Window Title]
Settings

[Main Instruction]
Error Applying Hard Drive Changes

[Content]
Failed to modify device 'Microsoft Virtual Hard Disk'.

Cannot open attachment 'D:\&65Pn02.2.vhd'. Error: 'The file or directory is corrupted and unreadable.'

Cannot get information for attachment 'D:\&65Pn02.2.vhd'

Cannot open attachment 'D:\&65Pn02.2.vhd'. Error: 'The file or directory is corrupted and unreadable.'

[Expanded Information]
'NewMachine' failed to modify device 'Microsoft Virtual Hard Disk'. (Virtual machine ID D8D73511-6D6B-4604-A09B-BA4F5CD35206)

'NewMachine': Cannot open attachment 'D:\&65Pn02.2.vhd'. Error: 'The file or directory is corrupted and unreadable.' (0x80070570). (Virtual machine ID D8D73511-6D6B-4604-A09B-BA4F5CD35206)

'NewMachine': Cannot get information for attachment 'D:\&65Pn02.2.vhd'. (Virtual machine ID D8D73511-6D6B-4604-A09B-BA4F5CD35206)

'NewMachine': Cannot open attachment 'D:\&65Pn02.2.vhd'. Error: 'The file or directory is corrupted and unreadable.' (0x80070570). (Virtual machine ID D8D73511-6D6B-4604-A09B-BA4F5CD35206)

[V] See details Â [Close]"</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->