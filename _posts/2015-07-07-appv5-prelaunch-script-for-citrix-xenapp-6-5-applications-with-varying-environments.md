---
id: 556
title: 'AppV5 - Prelaunch Script for Citrix & 6.5 applications with varying environments'
date: 2015-07-07T22:44:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/07/appv5-prelaunch-script-for-citrix-&-6-5-applications-with-varying-environments/
permalink: /2015/07/07/appv5-prelaunch-script-for-citrix-&-6-5-applications-with-varying-environments/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/appv5-prelaunch-script-for-citrix.html
blogger_internal:
  - /feeds/7977773/posts/default/4124007177355135326
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - PowerShell
  - scripting
---
We utilize a lot of pre-launch scripts for our AppV5 applications that we use in our Citrix & 6.5 environment. Â They become a necessity very quickly as AppV5 stores the executable down a very long path. Â Citrix & 6.5 has a maximum launch string of 160 characters and this maximum prevents a lot of applications from working if they require parameters to be passed to them. Â An example looks like this:

<div>
  <pre class="lang:batch decode:true ">"C:\ProgramData\Microsoft\AppV\Client\Integration\D8E3DB68-4E48-4409-8E95-4354CC6E664B\Root\VFS\ProgramFilesX64\dlc11.2\bin\prowin32.exe" -p \\wsfsc01pharm\CentricityPharmacy\rx\v92\cfg\cfgstart.p -param S,%CLIENTNAME%,crh1214%env%,CITRIX,rxpv91cal,10920 -wy</pre>
</div>

<div>
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
    This launch path is too long for & 6.5. Â The string will be truncated and the program will fail to launch properly. Â We have several environments that work with the same package files so we set them as variables. Â To get this package to launch properly we create a prelaunch script that looks like so:
  </div>
</div>

<div>
</div>

<div>
  <pre class="lang:batch decode:true ">:============================================================================
:            AHS BDM Pharmacy prelaunch script
:            By Trentent Tye
:            2015-05-08
:
:            This script will take 2 parameters.  The location (CAL, EDM, ACB)and the environment
:            -T for test, -P for Prod and -N for training. If no parameter is passed it will assume production.
:
:            eg, AHS-BDMPHARMACY EDM N
 
@ECHO OFF
 
SET ENV=%2
SET FILE=%RANDOM%
 
IF /I [%1] == [CAL] (
  ECHO Launching BDM...
   > "%TEMP%\%FILE%.CMD" ECHO @ECHO OFF
  >> "%TEMP%\%FILE%.CMD" ECHO START "" "c:\Program Files\DLC11.2\bin\prowin32.exe" -p \\wsfsc01pharm\CentricityPharmacy\rx\v92\cfg\cfgstart.p -param S,%CLIENTNAME%,crh1214%env%,CITRIX,rxpv91cal,10920 -wy
)
 
IF /I [%1] == [EDM] (
  ECHO Launching BDM...
   > "%TEMP%\%FILE%.CMD" ECHO @ECHO OFF
  >> "%TEMP%\%FILE%.CMD" ECHO START "" "c:\Program Files\DLC11.2\bin\prowin32.exe" -p \\chfs\apps\centricitypharmacy\rx\v92\cfg\cfgstart.p -param S,%CLIENTNAME%,ahe1271%env%,citrix,rx11gproddb,11920 -wy
)
 
IF /I [%1] == [ACB] (
  ECHO Launching BDM...
   > "%TEMP%\%FILE%.CMD" ECHO @ECHO OFF
  >> "%TEMP%\%FILE%.CMD" ECHO START "" "c:\Program Files\DLC11.2\bin\prowin32.exe" -p \\nafsprog\pharmapp\rx\v92\cfg\cfgstart.p -param S,%CLIENTNAME%,acb1240%env%,citrix,acbrxpv92,10920 -wy
 
)
 
:RELAUNCH
 
 
cmd.exe /c "%TEMP%\%FILE%.CMD" /appvve:d8e3db68-4e48-4409-8e95-4354cc6e664b_c2342321-21e9-4e1f-ac2e-adf679020d55</pre>
</div>

<div>
</div>

<div>
</div>

<div>
  At this point we set our application to point to this CMD file.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-S9IeJRLsrFs/VZypf-RWNZI/AAAAAAAAA50/G3ZSgUtseKQ/s1600/Screen%2BShot%2B2015-07-07%2Bat%2B10.39.03%2BPM.png"><img src="http://3.bp.blogspot.com/-S9IeJRLsrFs/VZypf-RWNZI/AAAAAAAAA50/G3ZSgUtseKQ/s320/Screen%2BShot%2B2015-07-07%2Bat%2B10.39.03%2BPM.png" width="320" height="247" border="0" /></a>
</div>

<div>
</div>

<div>
  And the application will launch with the shorter string with the ability for us to change the environment and location quickly and easily.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->