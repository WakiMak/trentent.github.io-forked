---
id: 665
title: Internet Explorer popping up outside the App-V Bubble
date: 2013-01-15T16:31:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/01/15/internet-explorer-popping-up-outside-the-app-v-bubble/
permalink: /2013/01/15/internet-explorer-popping-up-outside-the-app-v-bubble/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/01/internet-explorer-popping-up-outside.html
blogger_internal:
  - /feeds/7977773/posts/default/7824349800432852016
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Internet Explorer
---
We have an application that launches Internet Explorer for printing certain PDF documents that it creates. Adobe Reader is in the AppV package and NOT locally on the server. The application resides in an AppV bubble but Internet Explorer starts outside the bubble. I was able to verify that by starting the application with the debug switch "/exe cmd.exe" and running SET which shows some variables that only exist within an AppV bubble. Once at the command prompt I launched the application and got to the point where it launched IE, from there I was able to use the IE open dialog to launch cmd.exe from the Windowssystem32 folder. Once that was open I ran SET again and the AppV variables were not there; this confirmed that it was not in the AppV bubble.

It appears this issue is caused by & because it modifies the Internet Explorer handler registry keys to point to a unique Citrix version of IE. This version appears to open outside the bubble. to correct this you need to change these registry keys:

<div style="font-family: 'Courier New',Courier,monospace;">
  <pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\Software\Classes\http\shell\open\command]
@="\"C:\\Program Files (x86)\\Internet Explorer\\iexplore.exe\" \"%1\""

[HKEY_LOCAL_MACHINE\Software\Classes\https\shell\open\command]
@="\"C:\\Program Files (x86)\\Internet Explorer\\iexplore.exe\" \"%1\""

[HKEY_LOCAL_MACHINE\Software\Classes\htmlfile\shell\opennew\command]
@="\"C:\\Program Files (x86)\\Internet Explorer\\iexplore.exe\" \"%1\""

[HKEY_LOCAL_MACHINE\Software\Classes\htmlfile\shell\open\command]
@="\"C:\\Program Files (x86)\\Internet Explorer\\iexplore.exe\" \"%1\""</pre>
</div>

With the keys set like that Internet Explorer will now launch within the AppV bubble.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->