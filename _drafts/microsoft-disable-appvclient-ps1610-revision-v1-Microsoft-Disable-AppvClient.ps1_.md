---
id: 1064
title: Microsoft Disable-AppvClient.ps1
date: 2016-04-24T22:45:37-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/610-revision-v1/
permalink: /2016/04/24/610-revision-v1/
---
Microsoft has noticed that AppV 5 sometimes has trouble cleaning up after itself and it looks like Microsoft has made a cleanup powershell script for AppV5. Â It is located here:  
C:\Program Files\Microsoft Application Virtualization\Client

The command needs to be executed like so:

<pre class="lang:ps decode:true ">.\Disable-AppVClient.ps1 -ModulePath "C:\Program Files\Microsoft Application Virtualization\Client\AppvClient\AppvClient.psd1" -RemoveAllPackages -PackageInstallationRoot "D:\AppVData\PackageInstallationRoot"</pre>

This powershell script will also inform you if you need to remove the packages manually because of permissions issues. Â I would highly recommend putting it in the shutdown script of your servers/desktops.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->