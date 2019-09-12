---
id: 604
title: Extend disk space on a VMWare PVS system
date: 2014-07-14T10:25:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/07/14/extend-disk-space-on-a-vmware-pvs-system/
permalink: /2014/07/14/extend-disk-space-on-a-vmware-pvs-system/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/07/extend-disk-space-on-vmware-pvs-system.html
blogger_internal:
  - /feeds/7977773/posts/default/2186118669474841219
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerShell
  - Provisioning Services
  - scripting
  - Target Device
  - VMWare
---

```powershell
#############################################################################################################
#
#  By: Trentent Tye - Intel Server
#
#  Date: 2014-07-14
#
#  Comment: This script was written to extend the available free space on Citrix PVS servers.  It uses
#           a text file with the list of servers that need their disk space extended.  It assumes disk 0
#           needs extended by default because that's the only disk on a provisioned server.  Use with
#           caution against other servers.
#
#
##############################################################################################################

$executingScriptDirectory = Split-Path -Path $MyInvocation.MyCommand.Definition -Parent
pushd $executingScriptDirectory
cd $executingScriptDirectory

$servers = get-content serverlist.txt

foreach ($server in $servers) {
   Get-HardDisk -vm $server | Set-HardDisk -CapacityGB 40 -ResizeGuestPartition -Confirm:$false
   echo "select volume 0" | out-file  -FilePath \\$server\c$\swinst\diskpart.txt  -Encoding ASCII  -force
   echo "extend" | out-file  -FilePath \\$server\c$\swinst\diskpart.txt  -Encoding ASCII -Append  -force
   .\psexec.exe -accepteula \\$server diskpart.exe /s C:\swinst\diskpart.txt

```


&nbsp;


```plaintext
########################
ServerList.txt
Server1
Server2
Server3
######################
```


&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->