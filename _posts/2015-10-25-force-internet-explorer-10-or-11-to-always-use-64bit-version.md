---
id: 541
title: Force Internet Explorer 10 or 11 to always use 64bit version
date: 2015-10-25T23:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/10/25/force-internet-explorer-10-or-11-to-always-use-64bit-version/
permalink: /2015/10/25/force-internet-explorer-10-or-11-to-always-use-64bit-version/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/10/force-internet-explorer-10-or-11-to.html
blogger_internal:
  - /feeds/7977773/posts/default/2453331354299793459
categories:
  - Blog
  - Uncategorized
tags:
  - Internet Explorer
---
I was working on an issue where a user was always prompted to &#8216;Install&#8217; the Citrix ICA client. Â No matter how many times they downloaded and installed the client it continuously prompted them to install it again from the Web Interface:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-Upy64Kt1Gvk/Vie_GX0jWBI/AAAAAAAABJ8/rd_vs5XSGh4/s1600/2.PNG"><img src="http://2.bp.blogspot.com/-Upy64Kt1Gvk/Vie_GX0jWBI/AAAAAAAABJ8/rd_vs5XSGh4/s320/2.PNG" width="320" height="171" border="0" /></a>
</div>

I checked the add-on&#8217;s and saw the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-LlryzM0kFx0/Vie_jfLAllI/AAAAAAAABKE/VZW2Ow2h4dY/s1600/3.PNG"><img src="http://4.bp.blogspot.com/-LlryzM0kFx0/Vie_jfLAllI/AAAAAAAABKE/VZW2Ow2h4dY/s320/3.PNG" width="320" height="158" border="0" /></a>
</div>

No Citrix plugins in site. Â I then checked Task Manager to confirm the IE type (32bit vs 64bit) and this is what I saw:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-MrHj9nMfiQA/Vie_6jowcCI/AAAAAAAABKM/sJS-KSe8P4w/s1600/4.PNG"><img src="http://4.bp.blogspot.com/-MrHj9nMfiQA/Vie_6jowcCI/AAAAAAAABKM/sJS-KSe8P4w/s320/4.PNG" width="320" height="148" border="0" /></a>
</div>

Without the *32, Internet Explorer is running in 64bit mode. Â Currently, Citrix does not provide a 64bit plugin to IE so it won&#8217;t run and it won&#8217;t be detected. Â I then exited IE and browsed to the Internet Explorer folder (C:program files (x86)Internet Exploreriexplore.exe) and attempted to launch iexplore.exe from there. Â Still came up as 64bit. Â So now this got interesting&#8230; Â [Microsoft does not allow or provide a way to force a 64bit default for IE on Windows 7.](http://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/make-internet-explorer-64-bit-the-default-to-run/7f59d57a-14d9-4c95-b339-bc03631e4047?auth=1)

So how is this happening?

It turns out there is a registry key you can set that will force IE to ALWAYS be 64bit:

<span style="background-color: white; font-family: 'Segoe UI', 'Segoe UI Web', 'Segoe UI Symbol', 'Helvetica Neue', 'BBAlpha Sans', 'S60 Sans', Arial, sans-serif; font-size: 15px;"><a href="https://support.microsoft.com/en-us/kb/2716529">HKEY_CURRENT_USER\Software\Microsoft\Internet Explorer\Main\TabProcGrowth</a> (or HKLM)</span>

If the REG_DWORD is 0x0 it will always force IE to be 64bit. Â Deleting or changing this value will default IE to 32bit. Â So this key \*could\* be used to force IE to be 64bit. Â There is a potential issue to be aware of, this will force IE to use the same process as the launcher for tabs, as opposed to spanning new processes. Â Whether that increases/decreases stability would be something you&#8217;d have to test.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->