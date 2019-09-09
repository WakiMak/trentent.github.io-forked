---
id: 2397
title: AppV5 - System Environment Variables Gotcha's
date: 2017-06-05T09:15:16-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2397
permalink: /2017/06/05/appv5-system-environment-variables-gotchas/
image: /wp-content/uploads/2017/06/AppV_Set.png
categories:
  - Blog
tags:
  - AppV
---
We had an application that has its environment configured via 'system environment variables'.  System environment variables differ from 'user' environment variables in that they are global so all users see them, and in a native Windows environment, they are stored in a different location; but still in the registry.  So it seemed, like everything else App-V, these values would be captured (they are just registry values).  Imagine our surprise when they were \*not\* captured.

<img class="aligncenter size-large wp-image-2398" src="http://theorypc.ca/wp-content/uploads/2017/06/AppV_vs_Registry-1600x460.png" alt="" width="1140" height="328" srcset="http://theorypc.ca/wp-content/uploads/2017/06/AppV_vs_Registry.png 1600w, http://theorypc.ca/wp-content/uploads/2017/06/AppV_vs_Registry-300x86.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/AppV_vs_Registry-768x221.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

The tech who was working on this thought this must be a mistake, exported the registry keys he needed into a file and imported them into the package:

<img class="aligncenter size-full wp-image-2399" src="http://theorypc.ca/wp-content/uploads/2017/06/Import.png" alt="" width="225" height="206" /> 

&nbsp;

<img class="aligncenter size-full wp-image-2400" src="http://theorypc.ca/wp-content/uploads/2017/06/sys.reg_..png" alt="" width="753" height="645" srcset="http://theorypc.ca/wp-content/uploads/2017/06/sys.reg_..png 753w, http://theorypc.ca/wp-content/uploads/2017/06/sys.reg_.-300x257.png 300w" sizes="(max-width: 753px) 100vw, 753px" /> 

&nbsp;

<img class="aligncenter size-full wp-image-2401" src="http://theorypc.ca/wp-content/uploads/2017/06/Sys_Env_In_Reg.png" alt="" width="733" height="227" srcset="http://theorypc.ca/wp-content/uploads/2017/06/Sys_Env_In_Reg.png 733w, http://theorypc.ca/wp-content/uploads/2017/06/Sys_Env_In_Reg-300x93.png 300w" sizes="(max-width: 733px) 100vw, 733px" /> 

&nbsp;

He created a startup script that would modify the environment variables to whatever environment the user wanted to launch (Test, Prod, Dev, etc.).

<img class="aligncenter size-full wp-image-2402" src="http://theorypc.ca/wp-content/uploads/2017/06/AppPrelaunch.cmd_.png" alt="" width="932" height="471" srcset="http://theorypc.ca/wp-content/uploads/2017/06/AppPrelaunch.cmd_.png 932w, http://theorypc.ca/wp-content/uploads/2017/06/AppPrelaunch.cmd_-300x152.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/AppPrelaunch.cmd_-768x388.png 768w" sizes="(max-width: 932px) 100vw, 932px" /> 

BUT, no matter what was set the application always launched with the parameters it was sequenced with.  In this case it was always PROD.  We would put a 'SET' or ECHO '%TT_SYS1%' or some such into the batch file, _after_ the 'SET/SETX' commands we could see the _variables were different_.  They were what we expected them to be.  But **<span style="text-decoration: underline;">launching</span>** a new process with these changed variables resulted with the new process _**inheriting the <span style="text-decoration: underline;">original</span> environment variables. **_ So it was always PROD.  Now, these are SYSTEM environment variables and Microsoft released a utility, [setx](https://technet.microsoft.com/en-us/library/cc755104(v=ws.11).aspx), which you can use to manipulate them.  This utility still did not work as expected, the [variables were not being retained by a child process](https://msdn.microsoft.com/en-us/library/windows/desktop/ms682653(v=vs.85).aspx).

"By default, a child process inherits the environment variables of its parent process."

...

When this was brought to my attention I suggested we explore 'Exporting the manifest and seeing what was stored there'. Changing the registry entries in the package did NOTHING.  So I suspected they weren't being used at all...

What we found is when we extracted the manifest file:

<img class="aligncenter size-full wp-image-2403" src="http://theorypc.ca/wp-content/uploads/2017/06/ENV_Manifest.png" alt="" width="772" height="496" srcset="http://theorypc.ca/wp-content/uploads/2017/06/ENV_Manifest.png 772w, http://theorypc.ca/wp-content/uploads/2017/06/ENV_Manifest-300x193.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/ENV_Manifest-768x493.png 768w" sizes="(max-width: 772px) 100vw, 772px" /> 

We saw where the environment variables where being stored.  By deleting this section and reimporting the manifest file we found that we could set environment variables and they would _correctly_ inherit from the parent process from then on.  But if you do NOT delete this section of the Manifest file, then whatever is set for that AppV session will be <span style="text-decoration: underline;">reset for any new processes</span> created in the bubble.

* * *

&nbsp;

Final Analysis:

AppV5 treats Environment Variables differently then one would expect from a native system.  My expectation when I enter the bubble is an environment that I could manipulate and then have those changes persist for the duration of that session.  Instead, AppV environment variables are reset for EVERY process in the bubble.  Although you can manipulate them in the current process, creating a new process (cmd.exe/powershell.exe/whatever.exe) results in those changes being reset for variables specified in the manifest file.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
