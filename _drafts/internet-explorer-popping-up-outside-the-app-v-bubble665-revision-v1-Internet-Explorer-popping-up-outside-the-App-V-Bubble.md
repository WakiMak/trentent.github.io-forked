---
id: 1146
title: Internet Explorer popping up outside the App-V Bubble
date: 2016-04-25T08:52:17-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/665-revision-v1/
permalink: /2016/04/25/665-revision-v1/
---
We have an application that launches Internet Explorer for printing certain PDF documents that it creates.Â Adobe Reader is in the AppV package and NOT locally on the server.Â The application resides in an AppV bubble but Internet Explorer starts outside the bubble.Â I was able to verify that by starting the application with the debug switch &#8220;/exe cmd.exe&#8221; and running SET which shows some variables that only exist within an AppV bubble.Â Once at the command prompt I launched the application and got to the point where it launched IE, from there I was able to use the IE open dialog to launch cmd.exe from the Windowssystem32 folder.Â Once that was open I ran SET again and the AppV variables were not there; this confirmed that it was not in the AppV bubble.

It appears this issue is caused by XenApp because it modifies the Internet Explorer handler registry keys to point to a unique Citrix version of IE.Â This version appears to open outside the bubble.Â to correct this you need to change these registry keys:

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