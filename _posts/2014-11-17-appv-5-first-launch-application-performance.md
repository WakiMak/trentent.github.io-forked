---
id: 587
title: AppV 5 first launch application performance
date: 2014-11-17T17:22:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/11/17/appv-5-first-launch-application-performance/
permalink: /2014/11/17/appv-5-first-launch-application-performance/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/11/appv-5-first-launch-application.html
blogger_internal:
  - /feeds/7977773/posts/default/4413147536800346263
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Performance
  - scripting
---
Our AppV 5 environment is a full infrastructure implementation. Â We utilize the management/streaming server to pull the applications down to our Citrix & 6.5 servers. Â Our & servers are Citrix PVS servers, we enable the Cache on RAM with disk overflow and the write-cache intermediate mode. Â To maximize CPU performance we have our ESXi hosts set to maximum performance and disable power management in the BIOS of the hosts. Â We have some applications that are very latency sensitive and the switching of power states on the ESXi hosts have caused performance degradation so we have power management disabled. Â We have setup our PVS servers with the secondary D: WriteCache disk where we fully mount the AppV 5 packages, removing the streaming latency that going over a network may add.

Because of some performance concerns with the Shared Content Store (SCS) I was tasked with coming up with a way of determining if there is a performance impact of switching from fully mounted applications. Â In order to determine the impact my plan was to measure a baseline based on disk performance. Â Our SMB share that we are storing our .appv packages actually has the same performance as the local disk. Â Since AppV packages are immutable, the only performance consideration we should be concerned is READ performance from the SMB share compared to the local disk. Â The writes occur in the %userprofile%appdatavfs which is stored on the C:\ drive. Â The Cache to RAM with disk overflow feature would ensure that write performance into those directories are fast and should be near instant.

With that said, I've used the [diskspd.exe](http://blogs.technet.com/b/josebda/archive/2014/10/13/diskspd-powershell-and-storage-performance-measuring-iops-throughput-and-latency-for-both-local-disks-and-smb-file-shares.aspx) application (new from Microsoft) to measure performance. AppV 5 utilizes a [64KB](http://trentent.blogspot.ca/2014/08/appv-5-minimum-mounted-file-size-is.html) allocation size so that's what we'll set as our -b value. Â We'll measure Latency statistics comparing local disk to the file share as well.

D:\diskspd.exe -c1G -b64K -L -d60 D:test.dat  
D:\diskspd.exe -c1G -b64K -L -d60 \citrixnas01ctx\_images\_testtest.dat

Results:  
D:\

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-Z94ZytlmMT8/VELWr38H-II/AAAAAAAAAkQ/c58Y9ZZXb9Q/s1600/Screen%2BShot%2B2014-10-18%2Bat%2B3.07.08%2BPM.png"><img src="http://2.bp.blogspot.com/-Z94ZytlmMT8/VELWr38H-II/AAAAAAAAAkQ/c58Y9ZZXb9Q/s1600/Screen%2BShot%2B2014-10-18%2Bat%2B3.07.08%2BPM.png" width="320" height="232" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

SMB share:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-8Sh5c5HGNCQ/VELW1tXjgjI/AAAAAAAAAkY/eFxEZwVtsv0/s1600/Screen%2BShot%2B2014-10-18%2Bat%2B3.06.46%2BPM.png"><img src="http://2.bp.blogspot.com/-8Sh5c5HGNCQ/VELW1tXjgjI/AAAAAAAAAkY/eFxEZwVtsv0/s1600/Screen%2BShot%2B2014-10-18%2Bat%2B3.06.46%2BPM.png" width="320" height="194" border="0" /></a>
</div>

&nbsp;

<pre class="lang:default decode:true ">D:
Read IO
thread |       bytes     |     I/Os     |     MB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |     83828801536 |      1279126 |    1332.13 |   21314.08 |    0.045 |     0.138 | D:\test.dat (1024MB)
-----------------------------------------------------------------------------------------------------
total:       83828801536 |      1279126 |    1332.13 |   21314.08 |    0.045 |     0.138

SMB:
Read IO
thread |       bytes     |     I/Os     |     MB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |     80124379136 |      1222601 |    1273.26 |   20372.15 |    0.096 |     0.105 | \\citrixnas01\citrix_test\test.dat (1024MB)
-----------------------------------------------------------------------------------------------------
total:       80124379136 |      1222601 |    1273.26 |   20372.15 |    0.096 |     0.105</pre>

Performance of the SMB share vs local disk:  
MB/s: 96%  
IO per s: 96%  
AvgLat: 46%

Based on these results, the local disk appears to be nearly identical to the SMB share with the average latency a little more than half on the local disk. Â Although it's half on average, we are still in the sub 1ms time range which is significantly faster than you could get with a physical server with a single local disk.

The next test I have is launching an application and getting to the splash screen to see how long it takes to load. Â For this test I've written a AutoIt script that takes two parameters, the name of the program to launch and the window title to monitor for.

<pre class="lang:autoit decode:true ">#Region ;**** Directives created by AutoIt3Wrapper_GUI ****
#AutoIt3Wrapper_Change2CUI=y
#EndRegion ;**** Directives created by AutoIt3Wrapper_GUI ****
;
; AutoIt Version: 3.0
; Language:       English
; Platform:       Win9x/NT
; Author:         Trentent Tye (trentent@ca.ibm.com)
;
; Script Function:
;   This script takes 2 parameters.  The first is the program to launch; second is the window we wait for then output
;   standard output with the duration both took.
#include 

$progToLaunch = $CmdLine[1]
$windowToWaitFor = $CmdLine[2]

Local $hTimer = TimerInit()
Run($progToLaunch)

WinWaitActive($windowToWaitFor)
Local $fDiff = TimerDiff($hTimer)
Local $Time = _NowTime()

ConsoleWrite($Time & "," & $fDiff & "," & $windowToWaitFor & "," & $progToLaunch & @CRLF)

;finished</pre>

I setup a cmd file with my program (Epic) because it takes some parameters prior to launch. Â I then pointed my timer application at it.

<pre class="lang:batch decode:true ">C:\timer.exe D:\AppVPerfTest\epic.cmd "Connection Status"</pre>

The results (with SCS):

<pre class="lang:default decode:true ">Net Stop / Net Start AppvClient
10:05:20 AM,196229.360505316,Connection Status,D:\AppVPerfTest\epic.cmd
10:05:51 AM,13964.6012970922,Connection Status,D:\AppVPerfTest\epic.cmd
10:05:59 AM,12841.0388750526,Connection Status,D:\AppVPerfTest\epic.cmd
10:06:03 AM,12334.0792614704,Connection Status,D:\AppVPerfTest\epic.cmd</pre>

The columns are Time Completed, Duration (in ms), Window to check for, Command executed.

After doing a Net Stop AppvClient / Net Start Appclient and then executing our AppV application it takes **<u>196</u>** seconds to start the application. Â After that initial launch it takes 12-15 seconds to start. Â Something is really dragging our initial application launch time down. Â I've found if I stop/start the service I need to do a **<u>add/publish</u>** via Powershell for that application to reduce the 196 seconds. Â This then takes first launch down to <u style="font-weight: bold;">48</u>Â seconds.Â This is how long is takes to start the same application after a system restart:

<pre class="lang:default decode:true ">3:25:41 PM,48195.8385074081,Connection Status,D:\AppVPerfTest\epic.cmd
3:27:26 PM,12646.0290344164,Connection Status,D:\AppVPerfTest\epic.cmd
3:27:45 PM,15450.5886222970,Connection Status,D:\AppVPerfTest\epic.cmd
3:28:00 PM,12488.5766906129,Connection Status,D:\AppVPerfTest\epic.cmd</pre>

<div>
</div>

First launch time after restart is 48 seconds then subsequent launches are essentially identical to just the stop/start appvclient service + add/publish. Â Which makes sense as our AppV5\_Data\_Precache script does a add/publish. Â Evidently, we're going to have to go further into AppV to understand what's causing it to take so long. Â To start, I'm going to detail our package a bit.

The application I'm testing this with is Epic. Â It's a huge application. Â AppXManifest is 72MB, FilesystemMetaData.xml is 1.7MB, Registry.Dat is 62MB.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-ovYMrk2DcNw/VEVcV2eYasI/AAAAAAAAAko/Hss3WqkZ3aI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.01.02%2BPM.png"><img src="http://3.bp.blogspot.com/-ovYMrk2DcNw/VEVcV2eYasI/AAAAAAAAAko/Hss3WqkZ3aI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.01.02%2BPM.png" width="320" height="100" border="0" /></a>
</div>

It contains 22,000 files totalling around 2GB in size.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-9_n4TT6S4Ig/VEVcatfnL-I/AAAAAAAAAkw/bqACLXylPwI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.02.01%2BPM.png"><img src="http://3.bp.blogspot.com/-9_n4TT6S4Ig/VEVcatfnL-I/AAAAAAAAAkw/bqACLXylPwI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.02.01%2BPM.png" width="246" height="320" border="0" /></a>
</div>

When AppV is "launching" the application for the first time it starts consuming memory and CPU for the 196 seconds that it's launching, peaking at nearly 600MB RAM and 50% CPU (though most of the time it's peaked at 25% CPU).

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-8hCDW6sPNTM/VEVctn0XM8I/AAAAAAAAAk4/fd6JP39pCwc/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.01.55%2BAM.png"><img src="http://4.bp.blogspot.com/-8hCDW6sPNTM/VEVctn0XM8I/AAAAAAAAAk4/fd6JP39pCwc/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.01.55%2BAM.png" width="320" height="42" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      AppV utilization before application launch
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-pZmchFhOjF4/VEVcttsUYiI/AAAAAAAAAk8/93SlMtd8Jms/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.04.16%2BAM.png"><img src="http://4.bp.blogspot.com/-pZmchFhOjF4/VEVcttsUYiI/AAAAAAAAAk8/93SlMtd8Jms/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.04.16%2BAM.png" width="320" height="52" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Start of application launch
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-vJWnP2T9FD8/VEVcz6AoJiI/AAAAAAAAAlQ/B1i51Gdfubs/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.04.37%2BAM.png"><img src="http://3.bp.blogspot.com/-vJWnP2T9FD8/VEVcz6AoJiI/AAAAAAAAAlQ/B1i51Gdfubs/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.04.37%2BAM.png" width="320" height="52" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Peak during launch
    </td>
  </tr>
</table>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-kfEg3jqMBug/VEVdA6f61TI/AAAAAAAAAlY/G-b1AeS0O9A/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.06.31%2BAM.png"><img src="http://3.bp.blogspot.com/-kfEg3jqMBug/VEVdA6f61TI/AAAAAAAAAlY/G-b1AeS0O9A/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B10.06.31%2BAM.png" width="320" height="50" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Application launched
    </td>
  </tr>
</table>

The AppV Debug logs do not give a whole lot of info as to what AppVClient.exe is doing during this time. Â Most of the logs show the application "start" as they setup their components, and when the application has launched. Â Almost all the logs show the first second or two of application launch and the last second or two before the GUI.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-GJeEI02l5vI/VEVdqsMXtgI/AAAAAAAAAls/ZixXGCIWp34/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.06.54%2BPM.png"><img src="http://2.bp.blogspot.com/-GJeEI02l5vI/VEVdqsMXtgI/AAAAAAAAAls/ZixXGCIWp34/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.06.54%2BPM.png" width="640" height="196" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      I launched the application at 12:36:24, it finally displayed the GUI at 12:39:38
    </td>
  </tr>
</table>

The only log that shows data during the entire time is the SHARED PERFORMANCE log. Â Unfortunately, the log is undecipherable to me.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-0xJVfQKtws4/VEVfKgxKyiI/AAAAAAAAAl8/Hd8WF0CEZWQ/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.14.05%2BPM.png"><img src="http://3.bp.blogspot.com/-0xJVfQKtws4/VEVfKgxKyiI/AAAAAAAAAl8/Hd8WF0CEZWQ/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.14.05%2BPM.png" width="640" height="312" border="0" /></a>
</div>

Lots of PreCreate, PreCleanup, PreAcquireForSection with no relevant data.

Perfmon.exe doesn't do a whole lot better with large gaps between file/process/network accesses:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-pMcw1ji8oS0/VEVjfYHovFI/AAAAAAAAAmM/8ZcaWWcHWXI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.22.34%2BPM.png"><img src="http://3.bp.blogspot.com/-pMcw1ji8oS0/VEVjfYHovFI/AAAAAAAAAmM/8ZcaWWcHWXI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.22.34%2BPM.png" width="640" height="104" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      What is it doing between 1:17:41 and 1:18:29? Â CPU is pegged but no disk activity
    </td>
  </tr>
</table>

Showing Registry accesses also shows huge gaps between the AppVClient.exe process accessing the system.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-p5_InJ2q704/VEVj9nA57MI/AAAAAAAAAmU/6QGl8KiUngI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.34.24%2BPM.png"><img src="http://2.bp.blogspot.com/-p5_InJ2q704/VEVj9nA57MI/AAAAAAAAAmU/6QGl8KiUngI/s1600/Screen%2BShot%2B2014-10-20%2Bat%2B1.34.24%2BPM.png" width="640" height="190" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Registry information still shows huge gaps in time where the AppVClient.exe is processing
    </td>
  </tr>
</table>

<div style="clear: both; text-align: center;">
</div>

So I'm not sure what the hold up is with regard to the delay for this application. Â None of the usual tools I use to monitor performance is giving me any hints or indications of why it's delaying launch.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-ACE6No9685o/VGqHhZGMYjI/AAAAAAAAAnY/p2CV5XAENTs/s1600/AppV_Net_Stop.gif"><img src="http://3.bp.blogspot.com/-ACE6No9685o/VGqHhZGMYjI/AAAAAAAAAnY/p2CV5XAENTs/s1600/AppV_Net_Stop.gif" width="320" height="218" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="text-align: center;">
  First launch delay when stopping/restarting AppV service.<br /> Total time is 221s for the first launch, 15s for subsequent launches
</div>

<div style="text-align: center;">
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-EA921bBVc8o/VGqHizuYk_I/AAAAAAAAAnc/WFK5QLZBa9s/s1600/2ndlaunch.gif"><img src="http://1.bp.blogspot.com/-EA921bBVc8o/VGqHizuYk_I/AAAAAAAAAnc/WFK5QLZBa9s/s1600/2ndlaunch.gif" width="320" height="229" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

&nbsp;

<div style="text-align: center;">
  First launch delay when starting application after a full system restart.<br /> Total time is 38s for first launch then 13s for subsequent launches.
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->