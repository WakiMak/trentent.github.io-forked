---
id: 635
title: Utilizing MCLI.exe to comment the newest version of a vDisk on Citrix Provisioning Services (PVS)
date: 2013-07-16T11:19:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/07/16/utilizing-mcli-exe-to-comment-the-newest-version-of-a-vdisk-on-citrix-provisioning-services-pvs/
permalink: /2013/07/16/utilizing-mcli-exe-to-comment-the-newest-version-of-a-vdisk-on-citrix-provisioning-services-pvs/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/07/utilizing-mcliexe-to-comment-newest.html
blogger_internal:
  - /feeds/7977773/posts/default/6502399215302078847
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - scripting
---
I've written this script to utilize MCLI.exe to add a comment to the newest version of a vDisk and have marked which fields correspond to what.

<pre class="lang:batch decode:true ">"C:\Program Files\Citrix\Provisioning Services\MCLI.exe" get diskversion -p disklocatorname=&65Tn03 sitename=SHW storename=& | FINDSTR /i /C:"version" > %TEMP%\diskver.txt

FOR /F "tokens=1-2 delims=: " %%A IN ('type %TEMP%\diskver.txt') DO set VERSIONN=%%B

"C:\Program Files\Citrix\Provisioning Services\MCLI.exe" set diskversion -p version=%VERSIONN% disklocatorname=&65Tn03 sitename=SHW storename=& -r description="Test"</pre>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-t8oT5gbKRI4/UeWAMe5sh_I/AAAAAAAAAW8/FfXpW2RwXGU/s1600/1.bmp"><img src="http://1.bp.blogspot.com/-t8oT5gbKRI4/UeWAMe5sh_I/AAAAAAAAAW8/FfXpW2RwXGU/s320/1.bmp" width="320" height="114" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-7Mn6Kp63GD8/UeWAM2-F85I/AAAAAAAAAXA/N18_eKkHSLM/s1600/2.bmp"><img src="http://4.bp.blogspot.com/-7Mn6Kp63GD8/UeWAM2-F85I/AAAAAAAAAXA/N18_eKkHSLM/s320/2.bmp" width="320" height="117" border="0" /></a>
</div>

This script can now be added to the "PVS Automatic" update feature to automatically comment the latest vDisk when it is updated.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->