---
id: 1044
title: Powershell script to update some sysinternals tools and wireshark
date: 2016-04-24T22:18:14-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/592-revision-v1/
permalink: /2016/04/24/592-revision-v1/
---
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
            Write-Host $File.Name “is out of date. Downloading new version…“    
            Copy-Item $file -Force
} #end If LastWriteTime
            else {
               Write-Host $File.Name “is up to date.“
} #end If LastWriteTime
        } #end Test-Path
    else {
        Write-Host $File.Name “is new. Downloading…“
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

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->