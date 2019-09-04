---
id: 1056
title: Extend disk space on a VMWare PVS system
date: 2016-04-24T22:36:19-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/604-revision-v1/
permalink: /2016/04/24/604-revision-v1/
---
<pre class="lang:ps decode:true ">#############################################################################################################
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
}</pre>

&nbsp;

<pre class="lang:default decode:true ">########################
ServerList.txt
Server1
Server2
Server3
#######################</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->