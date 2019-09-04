---
id: 995
title: 'AppV5  &#8211; Virtualizing and running Local Services stand-alone'
date: 2016-04-24T18:16:33-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/553-autosave-v1/
permalink: /2016/04/24/553-autosave-v1/
---
AppV5 allows you to virtualize Services that run under the LocalService account. Â The issue I&#8217;ve seen with this approach is AppV5 will load the service for a user upon <u>environment</u> launch (which is good for VDI / desktop deployments) and the service will terminate upon that AppV environment exiting. Â For remote desktop / Citrix XenApp deployments, this can be a big bother. Â If the service is required for running the application the standard answer is to extract it and install it using DeploymentConfig.xml or some other on application publish.

But what if you have a service that is NOT required to run an application, just needs to run in the background, AND can run as the LocalService? Â This service would be prime for being virtualized!

Do I have an example of such an application? Â Why yes I do.

Epic SystemPulse is an application that captures performance information and uploads it to a DB. Â It only runs under a LocalService account.

Using my [Citrix PVS Sequencer](http://trentent.blogspot.ca/2015/07/setting-up-pvs-appv5-sequencer.html), here is how I sequenced it:

These next screenshots illustrate the simplicity of this application:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-l1l2DE9JYiE/VZ9MO1OjpwI/AAAAAAAAA-I/TTwrTjLDk14/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B10.36.40%2BPM.png"><img src="http://3.bp.blogspot.com/-l1l2DE9JYiE/VZ9MO1OjpwI/AAAAAAAAA-I/TTwrTjLDk14/s320/Screen%2BShot%2B2015-07-09%2Bat%2B10.36.40%2BPM.png" width="320" height="168" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Virtual Registry Hive For Epic SystemPulse
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-4B5w--VJa4U/VZ9MO5980NI/AAAAAAAAA-E/yKxbUPs5mlk/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B10.36.58%2BPM.png"><img src="http://1.bp.blogspot.com/-4B5w--VJa4U/VZ9MO5980NI/AAAAAAAAA-E/yKxbUPs5mlk/s320/Screen%2BShot%2B2015-07-09%2Bat%2B10.36.58%2BPM.png" width="320" height="193" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      All files required for Epic SystemPulse
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-eiCzjbde2cQ/VZ9MO_oTT1I/AAAAAAAAA-A/Z2AZeioR1Kg/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B10.37.09%2BPM.png"><img src="http://1.bp.blogspot.com/-eiCzjbde2cQ/VZ9MO_oTT1I/AAAAAAAAA-A/Z2AZeioR1Kg/s320/Screen%2BShot%2B2015-07-09%2Bat%2B10.37.09%2BPM.png" width="320" height="57" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Lastly, the single service we need running
    </td>
  </tr>
</table>

Now, the problem.

I am running this service on a RDS/XenApp server. Â This service needs to run while an application, Epic Hyperspace, is running. Â My first thought was the use a connection group so that whenever Hyperspace is launched, SystemPulse will launch with it.

This, initially, worked. Â The first user who launched HyperSpace also had SystemPulse running at the same time. Â Subsequent user whom launched HyperSpace, had their SystemPulse service start and terminate, with the first service continuing to run. Â It looked like a success!

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-8eINs2dm2bE/VZ9RLQEKD0I/AAAAAAAAA-g/1ogdrglqBJw/s1600/Epic_System_Pulse.gif"><img src="http://4.bp.blogspot.com/-8eINs2dm2bE/VZ9RLQEKD0I/AAAAAAAAA-g/1ogdrglqBJw/s320/Epic_System_Pulse.gif" width="320" height="193" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Second user launches app, SystemPulse (EpicSvcMaster.exe & EpicSvcHost.exe) starts and terminates, the original processes still running.
    </td>
  </tr>
</table>

Eventually, that first user logged off. Â And the SystemPulse processes exited with it.

I thought being SYSTEM processes they were started in some way that a &#8216;SYSTEM&#8217; process wouldn&#8217;t terminate willy-nilly. Â This is not the case. Â A quick test of &#8216;get-appvclientpackage&#8217; shows the package as &#8216;In Use&#8217;, signifying that AppV5 is tracking the processes. Â The users logging off will turn that &#8216;True&#8217; into a &#8216;False&#8217;.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-gd3w0UeHOGM/VZ9SwCMN30I/AAAAAAAAA-s/iEoc-o56Ya8/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B11.04.52%2BPM.png"><img src="http://4.bp.blogspot.com/-gd3w0UeHOGM/VZ9SwCMN30I/AAAAAAAAA-s/iEoc-o56Ya8/s320/Screen%2BShot%2B2015-07-09%2Bat%2B11.04.52%2BPM.png" width="320" height="99" border="0" /></a>
</div>

Now, we had to wait until the next user started the application to have the service start back up. Â This was an unacceptable solution.

But we know that this AppV5 package will work as a service. Â We can reap all the benefits of AppV; we can deploy this package on a whim, it&#8217;s not a local/permanent install, it works! Â So now the question becomes how can we keep these advantages and what are the drawbacks?

The first drawback I encountered was &#8216;how do I open a AppV Virtual Environment (appvve) so this service will start?&#8217;

I tried several things.

I created a script/scheduled task that would check to see if the process was running and if not, open a appvve. Â This failed. Â I tried this using the LOCAL SERVICE to start my script to open my environment and it would not. Â I tried using the SYSTEM account and it failed. Â It turns out you can&#8217;t use either of these accounts to open a appvve.

With this failing, I moved to another method. Â AppV has the ability to start a script upon application publishing, could I use this to start my service when the application is published? Â It turns out that the account that runs when this context is started is the SYSTEM account, and it failed same as the scheduled task.

At this point I figured my problem is the account I&#8217;m trying to open the appvve with. Â I need to launch it with a service account. Â My first attempt I made a script with a hard-coded username/password string with PSEXEC.exe to open my appvve. Â I put this script in deploymentconfig.xml.

And it worked! Â It opened my appvve environment, the services started, and everything looked great! The only issue I had now is the hard-coded username/password combo. Â There is no way having that would be acceptable in a plain text file.

So I created a exe with AutoIt, &#8216;RunAsWait.exe&#8217;.

<pre class="lang:autoit decode:true ">;~ This file is used by PVS for the auto-update feature to enable it to run under a context that is not the MACHINE context
;~ By Trentent Tye May 02 2013
;~ The password in this plain text file has been obfuscated.  Please change it to the correct password
 
 if $CmdLine[1] = "/?" then 
 MsgBox(0x40000, 'Help', "Please execute this command with the path to a cmd or bat file." & @CRLF & "The file will execute with elevated credentials" & @CRLF & @CRLF & "eg, RunWait.exe C:\test.cmd")
  EndIf
   
RunAsWait ( "USERNAME", "DOMAIN", "PASSWORD", 1, "cmd.exe /c " & '"'&$CmdLine[1]&'"' ,"","","")</pre>

<div>
</div>

<div>
  It may not be the most secure thing when compiled, but internally it&#8217;s good enough&#8230;
</div>

<div>
</div>

<div>
  With this I set my deploymentconfig.xml with the program and arguments I was looking for:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-rFz2SrvWm6U/VZ9eOQ7f23I/AAAAAAAAA_E/AxrRjLNTfbI/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B11.38.20%2BPM.png"><img src="http://3.bp.blogspot.com/-rFz2SrvWm6U/VZ9eOQ7f23I/AAAAAAAAA_E/AxrRjLNTfbI/s320/Screen%2BShot%2B2015-07-09%2Bat%2B11.38.20%2BPM.png" width="320" height="52" border="0" /></a>
</div>

<div>
</div>

The CMD file looked like so:

<pre class="lang:batch decode:true ">:: ===========================================================================================================
::
:: Created by:  Trentent Tye
::   Intel Server Team
::   IBM Canada Ltd.
::
:: Creation Date: Nov 20, 2014
::
:: File Name:  runme.cmd
::
:: Description:  Launches Epic System Pulse
::                      Parameters accepted...   e.g. &lt;none&gt; and /RESTART
::
:: ===========================================================================================================
 
@ECHO OFF
CLS
 
IF /I [%1] EQU [/RESTART] (
    taskkill /im SystemPulseConfigEditor.exe /f
    taskkill /im EpicSvcHost.exe /f
    taskkill /im EpicSvcMaster.exe /f
 
  &gt; "%TEMP%\SYSPULSE.ps1" ECHO ipmo *appv*
 &gt;&gt; "%TEMP%\SYSPULSE.ps1" ECHO get-appvclientpackage Epic_SystemPulse_2014_x86 ^| Stop-AppvClientPackage -Global
 &gt;&gt; "%TEMP%\SYSPULSE.ps1" ECHO get-appvclientpackage Epic_SystemPulse_2014_x86 ^| Remove-AppvClientPackage
 &gt;&gt; "%TEMP%\SYSPULSE.ps1" ECHO Sync-AppvPublishingServer 1
 START "" powershell.exe -windowStyle hidden -executionPolicy byPass -command "&("%TEMP%\SYSPULSE.ps1")"
 exit
)
 
&gt; "%TEMP%\1.ps1" ECHO Start-Process -WindowStyle Hidden "D:\AppVData\PackageInstallationRoot\294A4DC7-2528-4C7E-832E-12D6053B4B6C\6A22D374-9682-4F48-B6B7-CC25BA16A943\Root\VFS\ProgramFilesX86\Epic\System Pulse\WindowsDataProvider\SystemPulseConfigEditor.exe"
 
ping 127.0.0.1 -n 10 &gt;NUL
::Check for SystemPulse process
:LOOP
 
START "" powershell.exe -windowStyle hidden -executionPolicy byPass -command "&("%TEMP%\1.ps1")"
ping 127.0.0.1 -n 30 &gt;NUL
 
REM need the second check as we can't escape the brackets in the first check
tasklist  /fi "IMAGENAME eq SystemPulseConfigEditor.exe" | findstr /i /c:"SystemPulse"
if '%ERRORLEVEL%' EQU '1' GOTO LOOP
 
EXIT /b 0</pre>

With this, my service will start upon application publish.

I set it to launch a exe that gets installed with the package (SystemPulseConfigEditor.exe) as I needed a unique name I could key in on to determine if the package started successfully. Â This has a drawback though, the exe I chose has a GUI and I launch the process with -WindowStyle Hidden; if the service crashes, this EXE prompts for attention. Â When RDP&#8217;ed into a server this notice comes up as &#8220;Interactive Service&#8221; something-or-other and clicking on it brings this exe visible. Â I have considered making a AutoIT app that has no GUI and just sits in the background doing nothing, but time has not permitted me this yet. Â Sometimes the SystemPulse service will crash so I added the /RESTART to this batch file to get it to kick off. Â By removing the package then &#8216;rsync&#8217;ing with the publishing server we trigger PublishPackage script which launches the service.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->