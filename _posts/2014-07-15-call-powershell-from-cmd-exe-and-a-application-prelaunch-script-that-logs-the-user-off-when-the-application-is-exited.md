---
id: 603
title: Call PowerShell from CMD.exe and a application-prelaunch script that logs the user off when the application is exited.
date: 2014-07-15T14:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/07/15/call-powershell-from-cmd-exe-and-a-application-prelaunch-script-that-logs-the-user-off-when-the-application-is-exited/
permalink: /2014/07/15/call-powershell-from-cmd-exe-and-a-application-prelaunch-script-that-logs-the-user-off-when-the-application-is-exited/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/07/call-powershell-from-cmdexe-and.html
blogger_internal:
  - /feeds/7977773/posts/default/7973462405706235032
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - scripting
---
<pre class="lang:ps decode:true ">&gt; "%TEMP%\proc_logoff.ps1" echo Start-Sleep -s 60
       &gt;&gt; "%TEMP%\proc_logoff.ps1" echo $localSession = Get-ChildItem 'HKCU:\Volatile Environment' -name
       &gt;&gt; "%TEMP%\proc_logoff.ps1" echo $process = get-process ^| where {$_.sessionID -eq $localSession} ^| where {$_.processName -eq "MetaVision"}
       &gt;&gt; "%TEMP%\proc_logoff.ps1" echo if ($process -eq $null) {logoff.exe}
       &gt;&gt; "%TEMP%\proc_logoff.ps1" echo $process.waitforexit()
       &gt;&gt; "%TEMP%\proc_logoff.ps1" echo logoff.exe
powershell.exe -ExecutionPolicy Unrestricted -WindowStyle Hidden -File "%TEMP%\proc_logoff.ps1"</pre>

I should comment this later, for now, this is the whole script.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->