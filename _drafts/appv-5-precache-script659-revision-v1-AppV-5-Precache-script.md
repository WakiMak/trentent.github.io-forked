---
id: 1139
title: AppV 5 Precache script
date: 2016-04-25T08:44:35-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/659-revision-v1/
permalink: /2016/04/25/659-revision-v1/
---
This script will go through and clear all AppV 5 packages then resync with a Streaming/Publishing server and then download the package locally to the server.

<pre class="lang:batch decode:true ">:: =========================================================================================================== 
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
    &gt;Appv5ClientRefresh-01.ps1 ECHO. 
    &gt;&gt;Appv5ClientRefresh-01.ps1 ECHO Import-Module AppVClient 
    &gt;&gt;Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientPackage * 
    &gt;&gt;Appv5ClientRefresh-01.ps1 ECHO Get-AppVPublishingServer ^| Sync-AppvPublishingServer  
    &gt;&gt;Appv5ClientRefresh-01.ps1 ECHO Mount-AppvClientPackage * 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\Appv5ClientRefresh-01.ps1 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted 
    DEL /F /Q Appv5ClientRefresh-01.ps1 &gt;NUL 
)</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->