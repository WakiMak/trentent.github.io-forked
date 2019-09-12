---
id: 659
title: AppV 5 Precache script
date: 2013-02-25T16:39:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/02/25/appv-5-precache-script/
permalink: /2013/02/25/appv-5-precache-script/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/02/appv-5-precache-script.html
blogger_internal:
  - /feeds/7977773/posts/default/4303949786400435864
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
This script will go through and clear all AppV 5 packages then resync with a Streaming/Publishing server and then download the package locally to the server.

```shell
:: =========================================================================================================== 
:: 
:: Created by:        Saman Salehian 
::            Intel Server Team 
::            IBM Canada Ltd. 
:: 
:: Creation Date:    Oct 16, 2012 
:: Modified Date:    Feb 25, 2013 -- Saman Salehian & Trentent Tye - Added load all applications sequentially - App-V 5 
:: 
:: File Name:        AppV_Data_PreCache.cmd 
:: 
:: Description:        Pre-Cache App-V Applications/Packages on XenApp Server 
:: 
:: =========================================================================================================== 

@ECHO OFF 
CLS 
 ================================================================================ 
:: Load all applications sequentially - App-V 5 
:: ================================================================================ 
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe ( 
    >Appv5ClientRefresh-01.ps1 ECHO. 
    >>Appv5ClientRefresh-01.ps1 ECHO Import-Module AppVClient 
    >>Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientPackage * 
    >>Appv5ClientRefresh-01.ps1 ECHO Get-AppVPublishingServer ^| Sync-AppvPublishingServer  
    >>Appv5ClientRefresh-01.ps1 ECHO Mount-AppvClientPackage * 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\Appv5ClientRefresh-01.ps1 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted 
    DEL /F /Q Appv5ClientRefresh-01.ps1 >NUL 
)
```

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->