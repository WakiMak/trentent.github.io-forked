---
id: 610
title: Microsoft Disable-AppvClient.ps1
date: 2014-05-06T11:19:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/05/06/microsoft-disable-appvclient-ps1/
permalink: /2014/05/06/microsoft-disable-appvclient-ps1/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/05/microsoft-disable-appvclientps1.html
blogger_internal:
  - /feeds/7977773/posts/default/4781544929581605256
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
Microsoft has noticed that AppV 5 sometimes has trouble cleaning up after itself and it looks like Microsoft has made a cleanup powershell script for AppV5.  It is located here:  
C:\Program Files\Microsoft Application Virtualization\Client

The command needs to be executed like so:


```powershell
.\Disable-AppVClient.ps1 -ModulePath "C:\Program Files\Microsoft Application Virtualization\Client\AppvClient\AppvClient.psd1" -RemoveAllPackages -PackageInstallationRoot "D:\AppVData\PackageInstallationRoot
```


This powershell script will also inform you if you need to remove the packages manually because of permissions issues.  I would highly recommend putting it in the shutdown script of your servers/desktops.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->