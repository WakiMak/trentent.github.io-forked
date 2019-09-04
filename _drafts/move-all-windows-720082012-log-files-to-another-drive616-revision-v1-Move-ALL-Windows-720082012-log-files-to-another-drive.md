---
id: 1071
title: Move ALL Windows 7/2008/2012 log files to another drive
date: 2016-04-24T22:53:09-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/616-revision-v1/
permalink: /2016/04/24/616-revision-v1/
---
<pre class="lang:ps decode:true "># ===========================================================================================================
#
# Created by:  Trentent Tye
#           Intel Server Team
#           IBM Canada Ltd.
#
# Creation Date: Apr 10, 2014
#
# File Name:  Move-Log-Files.ps1
#
# Description:  This script will be used to move all the log files from the default SYSTEMDRIVE to
#                   a drive of your choosing (in this case, D:\EventLogs).  We use this for non-persistent
#                   vDisks with a persistent write cache to keep these logs for troubleshooting purposes.
#
# ===========================================================================================================


# Change D:\EventLogs to some other destination if required.
$allLogs = Get-WinEvent -ListLog *
foreach ($logs in $allLogs) {
 $filename = split-path $logs.LogFilePath -leaf
 $logs.LogFilePath = “D:\EventLogs\$filename”
 $logs.SaveChanges()
}</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->