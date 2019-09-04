---
id: 616
title: Move ALL Windows 7/2008/2012 log files to another drive
date: 2014-04-10T09:59:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/04/10/move-all-windows-720082012-log-files-to-another-drive/
permalink: /2014/04/10/move-all-windows-720082012-log-files-to-another-drive/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/04/move-all-windows-720082012-log-files-to.html
blogger_internal:
  - /feeds/7977773/posts/default/5378114713322162235
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - 2012R2
  - PowerShell
  - scripting
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