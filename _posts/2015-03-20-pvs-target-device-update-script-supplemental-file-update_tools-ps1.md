---
id: 570
title: 'PVS Target Device Update Script - Supplemental File, Update_Tools.ps1'
date: 2015-03-20T15:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/pvs-target-device-update-script-supplemental-file-update_tools-ps1/
permalink: /2015/03/20/pvs-target-device-update-script-supplemental-file-update_tools-ps1/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/pvs-target-device-update-script_59.html
blogger_internal:
  - /feeds/7977773/posts/default/166163142956726064
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - procmon
---
This script is used to keep ProcMon.exe, ProcExp.exe and Wireshark up to date and available as tools we can use to troubleshoot our vDisks. � We store them within a folder on the C:Swinst.

<div>
</div>

<div>
  Update_Tools.ps1:
</div>

<div>
  <pre class="lang:ps decode:true ">#Update_Tools.ps1
# by Trentent Tye
# Updates processor monitor and processor explorer and wireshark
 
mkdir c:\swinst\Sysinternals
Set-Location c:\swinst\Sysinternals
 
$SysInternals=@()
$SysInternals+=ls \\live.sysinternals.com\tools\procmon.exe
$SysInternals+=ls \\live.sysinternals.com\tools\procexp.exe
foreach ($File in $SysInternals) {
    if (Test-Path $File.Name) {
        if ($File.LastWriteTime -ne (get-Item $File.Name).LastWriteTime) {
            Write-Host $File.Name "is out of date. Downloading new version…"   
            Copy-Item $file -Force
} #end If LastWriteTime
            else {
               Write-Host $File.Name "is up to date."
} #end If LastWriteTime
        } #end Test-Path
    else {
        Write-Host $File.Name "is new. Downloading…"
        Copy-Item $file -Force
} #end else Test-Path
} #end foreach $file
 
#remove current wireshark
$oldWireshark = dir "C:\swinst\wire*"
remove-item $oldWireshark -force
#download newest wireshark
$link = Invoke-WebRequest https://2.na.dl.wireshark.org/win32/
$wiresharkver = $link.links.innerText | select-string "Wire*" | select -last 1
 
$Source = "https://2.na.dl.wireshark.org/win32/" + $wiresharkver
$Destination = "c:\swinst\" + $wiresharkver
Invoke-WebRequest -uri $Source -OutFile $Destination
Unblock-File $Destination</pre>
  
  <p>
    &nbsp;
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->