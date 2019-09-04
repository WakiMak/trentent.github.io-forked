---
id: 1442
title: Inside the AppV 5 Sequencer
date: 2016-04-28T23:54:11-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/528-revision-v1/
permalink: /2016/04/28/528-revision-v1/
---
AppV5 sequencer

%TEMP%  
-.INGREDIENTS &nbsp;-xml file  
-.MANIFEST file  
-.STREAMMAP &#8211; xml file  
&nbsp;&#8212; deals with feature blocks  
-.REGISTRYHIVE file  
-.SHORTNAMEVERIFICATION  
-.PACKAGEHISTORY

??binary files??  
.log

/scratch subdirectory  
-.REGISTRYHIVE  
-.MANIFEST  
-.PACKAGEHISTORY

Two different sequence methods  
-Powershell  
-GUI

PowerShell execution:

Powershell.exe  
-vAppCollector.exe  
-cmd.exe  
-nppinstaller.exe  
-end of processing cmd.exe  
3:00 min wait period (waiting for post install tasks?)

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.23.51-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="122" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.23.51-2BPM-1-300x115.png" width="320" /></a>
</div>

-VAppCollector.exe

Process Tree

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.32.17-2BPM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="87" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.32.17-2BPM-1-300x81.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Process Tree
    </td>
  </tr>
</table>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: left;">
  1st &#8211; VAppCollector.exe &#8211; starts at 11:12:40, finishes at 11:12:42
</div>

<div style="clear: both; text-align: left;">
  What does it do?
</div>

<div style="clear: both; text-align: left;">
  Installs Microsoft Visual C++&nbsp;
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.41.58-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="13" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-02-29-2Bat-2B11.41.58-2BPM-1-300x13.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
  ??
</div>

<div style="clear: both; text-align: left;">
  VAppCollector.exe ends
</div>

<div style="clear: both; text-align: left;">
  powershell.exe continues at 11:12:42
</div>

<div style="clear: both; text-align: left;">
  creates 3 binary .log files
</div>

<div style="clear: both; text-align: left;">
  starts the install script (install.cmd)
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.02.16-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="134" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.02.16-2BAM-1-300x126.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
  Once the install.cmd ends there is a 3 minute delay before the sequencer starts to actually work
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.07.07-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="21" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.07.07-2BAM-1-300x20.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  After the delay, seems to read one of the .log files then scans file system, appears to look for either changes or *.lnk files
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.12.21-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="256" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.12.21-2BAM-1-300x241.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Once done scanning the directories, scans App Paths,
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.16.20-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="183" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.16.20-2BAM-1-300x172.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Appears to scan only the differences in HKCR
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.20.21-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="184" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.20.21-2BAM-1-300x173.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Scans Shell Extensions, the &#8220;Program Dir&#8221; in the registry then the Uninstall Key
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.23.32-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="176" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.23.32-2BAM-1-300x166.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Then it reads another binary log:
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.26.01-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="117" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.26.01-2BAM-1-300x110.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Then it scans the entire file system:
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.31.49-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="254" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.31.49-2BAM-1-300x238.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: left;">
  Then it saves everything to an ingredients file:
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.33.04-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="113" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.33.04-2BAM-1-300x106.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Scan all AppID
</div>

<div style="clear: both; text-align: left;">
  Reads another binary log
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  create Streammap
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.36.37-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="169" src="http://theorypc.ca/wp-content/uploads/2016/03/Screen-2BShot-2B2016-03-01-2Bat-2B12.36.37-2BAM-1-300x159.png" width="320" /></a>
</div>

<div style="clear: both; text-align: left;">
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->