---
id: 592
title: Powershell script to update some sysinternals tools and wireshark
date: 2014-09-04T15:00:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/09/04/powershell-script-to-update-some-sysinternals-tools-and-wireshark/
permalink: /2014/09/04/powershell-script-to-update-some-sysinternals-tools-and-wireshark/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/09/powershell-script-to-update-some.html
blogger_internal:
  - /feeds/7977773/posts/default/8718100613920419957
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - scripting
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
            Write-Host $File.Name "is out of date. Downloading new version..."    
            Copy-Item $file -Force
} #end If LastWriteTime
            else {
               Write-Host $File.Name "is up to date."
} #end If LastWriteTime
        } #end Test-Path
    else {
        Write-Host $File.Name "is new. Downloading..."
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