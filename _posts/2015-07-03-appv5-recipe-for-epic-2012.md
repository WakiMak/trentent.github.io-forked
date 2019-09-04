---
id: 557
title: 'AppV5 - Recipe for Epic 2012'
date: 2015-07-03T09:46:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/03/appv5-recipe-for-epic-2012/
permalink: /2015/07/03/appv5-recipe-for-epic-2012/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/appv5-recipe-for-epic-2012.html
blogger_internal:
  - /feeds/7977773/posts/default/3912730001148050556
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
Prerequisites:  
[AppV5 - Sequencing first steps](http://trentent.blogspot.ca/2015/07/appv5-sequencing-first-steps.html)

Recipe:

I create install.cmd files for all of my applications so that, if required in the future, I can re-sequence an application quickly completely through script or via one of those 'PowerShell AppV5 automated GUI's'.

<span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">install.cmd</span>

<pre class="lang:batch decode:true ">:: EPIC Install Script
::
:: By Trentent Tye
::
:: Assumptions: Adobe Reader is on the & server
:: Java Runtime Environment (JRE) is installed on the & Server
 
:: Must set Package Name to: Epic_Hyperspace_2012_7.9_RA1463_x86
:: Must modify C:\ProgramData\Epic\EPIC.CLI to point to path other than C:\Epic ("C:\Program Files (x86)\Epic\v7.9\Epic")
 
pushd %~dp0
cd /d %~dp0
 
cd Support\2012_Hyperspace_AI_Base
MsiExec /i "Epic 2012 Hyperspace.msi"  /qb!
 
::EPIC.CLI points to this location and requires these two files for configuration
mkdir "C:\Program Files (x86)\Epic\v7.9\Epic"
copy /y EpicComm.env "C:\Program Files (x86)\Epic\v7.9\Epic"
copy /y login.jpg  "C:\Program Files (x86)\Epic\v7.9\Epic"
 
::Install GDI update...
cd /d %~dp0
cd "Epic 2012 Install\GDI"
msiexec.exe /i "Epic 2012 GDI Update.msi" /qb /norestart
 
::For Canada we need to copy the localization to the en-CA folder.  This is fixed in 2014
mkdir "C:\Program Files (x86)\Epic\v7.9\en-CA"
xcopy /s /y "C:\Program Files (x86)\Epic\v7.9\en-US" "C:\Program Files (x86)\Epic\v7.9\en-CA"
 
ECHO Installing the client pack patch...
cd /d %~dp0
cd /d CP_021__RA1463
"InstallMSP.exe" /ICP "_RA1463_HYPERSPACE_1185.msp" /qb  /s
cd /d %~dp0
 
 
 
ECHO set the path in EPIC.CLI to "C:\Program Files (x86)\Epic\v7.9\Epic"
::notepad "C:\ProgramData\Epic\Config\EPIC.CLI"
  > "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO [General]
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO FormatVersion=2
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO IsSharedFile=0
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO UseEpicTelnet=1
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO ClientID=%%CLIENTNAME%%
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO SharedAppFiles=
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO ClientDefaultEnvID=
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO LookupByWSName=1
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO UseClientTimeZone=0
 >> "C:\ProgramData\Epic\Config\EPIC.CLI" ECHO EnvironmentFiles=C:\Program Files (x86)\Epic\v7.9\Epic\EpicComm.env
 
 
:Setup folder structure
mkdir C:\Users\Public\Desktop\MyApps\Epic
mkdir "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Epic"
copy /y "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Epic 2012\Hyperspace.lnk" "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Epic\Hyperspace 2012.lnk"
copy /y "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Epic 2012\Hyperspace.lnk" "C:\Users\Public\Desktop\MyApps\Epic\Hyperspace 2012.lnk"
rmdir /s /q "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Debugging Tools for Windows (x86)"
copy /y "%~dp0\Epic WorkflowTracer.lnk" "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Epic"
copy /y "%~dp0\Epic WorkflowTracer.lnk" "C:\Users\Public\Desktop\MyApps\Epic"
rmdir /s /q "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Epic 2012"
 
::new workflowtracer prelaunch script
copy /y "%~dp0\WorkFlowTracer.cmd" "C:\Program Files (x86)\Epic\v7.9\Shared Files"
 
:: Package must make a junction to a file share for the crashdumps to be saved to in deploymentconfig.xml
rmdir /s /q C:\CrashDumps</pre>

<div>
</div>

<div>
  Supplemental files:
</div>

<div>
</div>

<div>
  <span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">None</span>
</div>

<div>
  <span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">Â </span>
</div>

<div>
  <span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">Â </span>
</div>

<div>
  1) Select 'install.cmd' and click 'Next'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Ji_uGW-m8oA/VZYrDQq7OzI/AAAAAAAAA3E/_4-YtJzgyBk/s1600/20.PNG"><img src="http://1.bp.blogspot.com/-Ji_uGW-m8oA/VZYrDQq7OzI/AAAAAAAAA3E/_4-YtJzgyBk/s320/20.PNG" width="320" height="224" border="0" /></a>
</div>

<div>
</div>

<div>
  2) Name the package and click 'Next'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-m9p5P4o8LxI/VZYrQx4SZXI/AAAAAAAAA3M/q9EZ6JOj_Nc/s1600/21.PNG"><img src="http://4.bp.blogspot.com/-m9p5P4o8LxI/VZYrQx4SZXI/AAAAAAAAA3M/q9EZ6JOj_Nc/s320/21.PNG" width="320" height="224" border="0" /></a>
</div>

<div>
  3) Let the install script do its thing (note the clock)...</p> 
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Lx3khFd2wLY/VZbXSQknttI/AAAAAAAAA5g/gXoc2MWvPlA/s1600/Epic_Install.cmd.gif"><img src="http://4.bp.blogspot.com/-Lx3khFd2wLY/VZbXSQknttI/AAAAAAAAA5g/gXoc2MWvPlA/s400/Epic_Install.cmd.gif" width="400" height="260" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    4) AppV5 - <a href="http://trentent.blogspot.com/2015/07/appv5-post-install-sequencing-steps.html">Post install sequencing steps</a>
  </p>
  
  <p>
    5) Review for any extra registry keys/files (generally there are none or very few) and remove and save the package.
  </p>
  
  <p>
    6) In order for Epic to launch in a reasonable amount of time, <a href="http://trentent.blogspot.ca/2014/12/appv5-and-measuring-registrystaging.html">registry staging</a> must be done. Â Without a pre-executed registry staging, <a href="https://social.technet.microsoft.com/Forums/en-US/44944302-d8f3-4df1-b104-9c63345f88e0/poor-first-launch-performance-with-appv-5?forum=mdopappv">first launch performance of Epic can take hundreds of seconds</a>.
  </p>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-EA921bBVc8o/VGqHizuYk_I/AAAAAAAAAnc/WFK5QLZBa9s/s1600/2ndlaunch.gif"><img src="http://1.bp.blogspot.com/-EA921bBVc8o/VGqHizuYk_I/AAAAAAAAAnc/WFK5QLZBa9s/s1600/2ndlaunch.gif" width="320" height="229" border="0" /></a>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->