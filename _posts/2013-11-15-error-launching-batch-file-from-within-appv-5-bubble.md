---
id: 622
title: Error launching batch file from within AppV 5 bubble
date: 2013-11-15T15:37:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/11/15/error-launching-batch-file-from-within-appv-5-bubble/
permalink: /2013/11/15/error-launching-batch-file-from-within-appv-5-bubble/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/11/error-launching-batch-file-from-within.html
blogger_internal:
  - /feeds/7977773/posts/default/4551619578153704154
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
So we have an application that requires the "%CLIENTNAME%" variable to be passed to it in it's exe string.  The string looks like so:

```powershell
prowin32.exe -p \\nas\cfgstart.p -param S,%CLIENTNAME%,120n,citrix,a92,10920 -wy
```

<div>
</div>

<div>
  The issue we have is APPV does not seem to get that variable and pass it to the program.  So when the program starts, it makes %clientname% folders in a temp directory and we can't have two %clientname% folders in the same directory so only one instance of the application can be launched *period* if we do it this way, as opposed to one per server.
</div>

<div>
</div>

<div>
  To resolve this issue I wrote a script that will pass the %CLIENTNAME% variable to AppV by ripping it out of the registry:
</div>

<div>
</div>

```powershell
=======================================================
ECHO Launching Centricity...

for /f "tokens=1-3" %%A IN ('reg query "HKCU\volatile environment" /s ^| findstr /i /c:"CLIENTNAME"') DO SET CLIENTNAME=%%C

prowin32.exe -p \\nas\cfgstart.p -param S,%CLIENTNAME%,120n,citrix,a92,10920 -wy
=======================================================
```

<div>
</div>

<div>
  <p>
    This worked for AppV 4.6 without issue.  Now with AppV 5 I get an error, PATH NOT FOUND when trying to launch this script.
  </p>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://2.bp.blogspot.com/-pcAv4kNar4A/UoaUgP-y9bI/AAAAAAAAAa0/5rt17WcaMhA/s1600/Screen+Shot+2013-11-15+at+2.38.12+PM.png"><img src="http://2.bp.blogspot.com/-pcAv4kNar4A/UoaUgP-y9bI/AAAAAAAAAa0/5rt17WcaMhA/s320/Screen+Shot+2013-11-15+at+2.38.12+PM.png" width="320" height="29" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    To verify the path exists in the app I ran the following commands:
  </p>
</div>

<div>
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://3.bp.blogspot.com/-T97P6fP8v4s/UoaUhHhfdvI/AAAAAAAAAbA/euNanlROOQ0/s1600/Screen+Shot+2013-11-15+at+2.38.03+PM.png"><img src="http://3.bp.blogspot.com/-T97P6fP8v4s/UoaUhHhfdvI/AAAAAAAAAbA/euNanlROOQ0/s320/Screen+Shot+2013-11-15+at+2.38.03+PM.png" width="320" height="237" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    The powershell commands put me in the AppV 5 bubble then opened a command prompt.  From the command prompt I can see the directory that is missing.  Going back to procmon I was curious to see what command it was launching.  It was launching this:
  </p>
</div>

<div>
</div>


```powershell
cmd /c ""C:\ProgramData\Microsoft\AppV\Client\Integration\3230251A-5B8E-47EF-8378-986B2A492D05\Root\VFS\Common Desktop\MyApps\BDM Pharmacy v9.2\Centricity Pharmacy Test on rxpv91cal.cmd" /appvve:3230251A-5B8E-47EF-8378-986B2A492D05_03CA3F94-F318-4693-A7E3-038DB30E6C70"
```


<div>
</div>

<div>
  This command was failing.  It appears that when you are launching the .cmd file directly AppV 5 starts the cmd.exe *outside* the AppV bubble and it doesn't connect to the appvve.  To correct this I tried this command line:
</div>

<div>
</div>

```powershell
cmd /c "cmd.exe /c "C:\ProgramData\Microsoft\AppV\Client\Integration\3230251A-5B8E-47EF-8378-986B2A492D05\Root\VFS\Common Desktop\MyApps\BDM Pharmacy v9.2\Centricity Pharmacy Test on rxpv91cal.cmd" /appvve:3230251A-5B8E-47EF-8378-986B2A492D05_03CA3F94-F318-4693-A7E3-038DB30E6C70"
```

<div>
</div>

<div>
  <p>
    Success!  It launched successfully and saw the directory and everything was good there after.  So let that be a lesson to other AppV 5 package makers, if you need a pre-launch script you may need to modify your published icon to put another cmd.exe /c before the command file for it to start in the bubble.
  </p>
  
  <p>
    <a href="http://blogs.technet.com/b/virtualvibes/archive/2013/10/17/the-issues-of-sequencing-bat-shortcuts-in-app-v-5-0.aspx">A very good AppV blog</a> has already discovered this issue and came back with a better fix than mine:<br /> <a href="http://blogs.technet.com/b/virtualvibes/archive/2013/10/17/the-issues-of-sequencing-bat-shortcuts-in-app-v-5-0.aspx">http://blogs.technet.com/b/virtualvibes/archive/2013/10/17/the-issues-of-sequencing-bat-shortcuts-in-app-v-5-0.aspx</a>
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->