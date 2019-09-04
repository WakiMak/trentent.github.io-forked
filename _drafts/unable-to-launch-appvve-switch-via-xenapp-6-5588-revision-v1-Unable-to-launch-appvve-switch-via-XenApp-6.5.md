---
id: 1039
title: Unable to launch /appvve switch via XenApp 6.5
date: 2016-04-24T22:04:31-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/588-revision-v1/
permalink: /2016/04/24/588-revision-v1/
---
It appears you can&#8217;t launch an AppV environment around an application with the /appvve switch as Microsoft automatically strips the /appvve switch OR because icast.exe freaks out.

I have found an alternative method though:

<pre class="lang:batch decode:true">powershell.exe -command "&{$AppVName = Get-AppvClientPackage *APPNAME* ; Start-AppvVirtualProcess -AppvClientObject $AppVName cmd.exe}"</pre>

This will launch a cmd.exe window in the virtual environment in citrix with the passed .exe (cmd.exe in this example).

Dan mentioned another, less character way:

<pre class="lang:ps decode:true ">Start-AppvVirtualProcess -AppvClientObject (Get-AppvClientPackage *APPNAME*) cmd.exe</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->