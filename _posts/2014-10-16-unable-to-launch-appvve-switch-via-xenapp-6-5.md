---
id: 588
title: Unable to launch /appvve switch via & 6.5
date: 2014-10-16T01:35:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/10/16/unable-to-launch-appvve-switch-via-&-6-5/
permalink: /2014/10/16/unable-to-launch-appvve-switch-via-&-6-5/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/10/unable-to-launch-appvve-switch-via.html
blogger_internal:
  - /feeds/7977773/posts/default/4119960168136801195
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Citrix
  - scripting
  - &
---
It appears you can't launch an AppV environment around an application with the /appvve switch as Microsoft automatically strips the /appvve switch OR because icast.exe freaks out.

I have found an alternative method though:

<pre class="lang:batch decode:true">powershell.exe -command "&{$AppVName = Get-AppvClientPackage *APPNAME* ; Start-AppvVirtualProcess -AppvClientObject $AppVName cmd.exe}"</pre>

This will launch a cmd.exe window in the virtual environment in citrix with the passed .exe (cmd.exe in this example).

Dan mentioned another, less character way:

<pre class="lang:ps decode:true ">Start-AppvVirtualProcess -AppvClientObject (Get-AppvClientPackage *APPNAME*) cmd.exe</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->