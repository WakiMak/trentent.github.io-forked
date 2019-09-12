---
id: 619
title: Install AppV 5.0 Server via silent install
date: 2014-01-30T15:06:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/01/30/install-appv-5-0-server-via-silent-install/
permalink: /2014/01/30/install-appv-5-0-server-via-silent-install/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/01/install-appv-50-server-via-silent.html
blogger_internal:
  - /feeds/7977773/posts/default/8130825175028394951
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
I made a script to automate the creation of our AppV 5.0 Management and Publishing servers.  We are going to install them on the same system.  To do this, we already have a database so we are just adding new servers to the infrastructure.  This script also installs all prerequisites if required.

```shell
::================================================================================
::
:: Created by:  Trentent Tye
::   Intel Server Team
::
:: Creation Date: Jan 30, 2014
::
:: File Name:  RunME_Server.cmd
::
:: Description:  This script will configure a AppV 5 server for management
::                      and Streaming Capabilities
::
::================================================================================


@ECHO OFF
pushd %~dp0

CLS


:: ===========================================================================================================
:: Checks for Prerequisites
:: ===========================================================================================================
ECHO Checking for Prerequisites...

:: ===========================================================================================================
:: .NetFramework 4.0
:: ===========================================================================================================
SET DOTNET=1
IF EXIST "C:\Windows\Microsoft.NET\Framework\v4.0.30319\aspnet_regiis.exe" SET DOTNET=0

:: ===========================================================================================================
:: Microsoft Visual C++ 2010 x86
:: ===========================================================================================================
SET MSVC2010x86=1
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Products\D04BB691875110D32B98EBCF771AA1E1
IF '%ERRORLEVEL%' EQU '0' SET MSVC2010x86=0

:: ===========================================================================================================
:: Microsoft Visual C++ 2010 x64
:: ===========================================================================================================
SET MSVC2010x64=1
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Products\1926E8D15D0BCE53481466615F760A7F
IF '%ERRORLEVEL%' EQU '0' SET MSVC2010x64=0

:: ===========================================================================================================
:: Web Server + required features
:: ===========================================================================================================
        >FeatureCheck.ps1 ECHO.
        >>FeatureCheck.ps1 ECHO Import-Module servermanager
        >>FeatureCheck.ps1 ECHO get-windowsfeature web-* ^| where-object {$_.Installed -eq $True} ^| out-file $env:temp\windowFeature.txt
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\FeatureCheck.ps1
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
        DEL /F /Q FeatureCheck.ps1 >NUL

:: Find Web-Server
SET Web-Server=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Server"
IF '%ERRORLEVEL%' EQU '0' SET Web-Server=0

:: Find Web-Static-Content
SET Web-Static-Content=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Static-Content"
IF '%ERRORLEVEL%' EQU '0' SET Web-Static-Content=0

:: Find Web-Default-Doc
SET Web-Default-Doc=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Default-Doc"
IF '%ERRORLEVEL%' EQU '0' SET Web-Default-Doc=0

:: Find Web-Asp-Net
SET Web-Asp-Net=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Asp-Net"
IF '%ERRORLEVEL%' EQU '0' SET Web-Asp-Net=0

:: Find Web-Net-Ext
SET Web-Net-Ext=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Net-Ext"
IF '%ERRORLEVEL%' EQU '0' SET Web-Net-Ext=0

:: Find Web-ISAPI-Ext
SET Web-ISAPI-Ext=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-ISAPI-Ext"
IF '%ERRORLEVEL%' EQU '0' SET Web-ISAPI-Ext=0

:: Find Web-ISAPI-Filter
SET Web-ISAPI-Filter=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-ISAPI-Filter"
IF '%ERRORLEVEL%' EQU '0' SET Web-ISAPI-Filter=0

:: Find Web-Windows-Auth
SET Web-Windows-Auth=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Windows-Auth"
IF '%ERRORLEVEL%' EQU '0' SET Web-Windows-Auth=0

:: Find Web-Filtering
SET Web-Filtering=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Filtering"
IF '%ERRORLEVEL%' EQU '0' SET Web-Filtering=0

:: Find Web-Filtering
SET Web-Mgmt-Console=1
type %TEMP%\windowFeature.txt | findstr /I /C:"Web-Mgmt-Console"
IF '%ERRORLEVEL%' EQU '0' SET Web-Mgmt-Console=0


:: ===========================================================================================================
:: Windows Management Framework 3.0 (PowerShell 3.0)
:: ===========================================================================================================
        >WMF3.ps1 ECHO.
        >>WMF3.ps1 ECHO $osresult = $null 
        >>WMF3.ps1 ECHO $os = (Get-WmiObject Win32_OperatingSystem).Name 
        >>WMF3.ps1 ECHO if ($os -like "*Windows 8*") {$osresult = "True"} 
        >>WMF3.ps1 ECHO if ($os -like "*Server 2012*") {$osresult = "True"} 
        >>WMF3.ps1 ECHO $hfchk = Get-WmiObject -Query "select HotFixID from Win32_QuickFixEngineering where HotFixID like 'KB2506143'"
        >>WMF3.ps1 ECHO $hfchk.HotFixID ^|out-file $env:temp\WMF3.txt
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\WMF3.ps1
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
        DEL /F /Q WMF3.ps1 >NUL

SET WMF3=1
type %TEMP%\WMF3.txt | findstr /I /C:"KB2506143"
IF '%ERRORLEVEL%' EQU '0' SET WMF3=0


:: ===========================================================================================================
:: Install all prerequisites...
:: ===========================================================================================================

ECHO Results...
ECHO DOTNET=%DOTNET%
ECHO MSVC2010x86=%MSVC2010x86%
ECHO MSVC2010x64=%MSVC2010x64%
ECHO Web-Server=%Web-Server%
ECHO Web-Static-Content=%Web-Static-Content%
ECHO Web-Default-Doc=%Web-Default-Doc%
ECHO Web-Asp-Net=%Web-Asp-Net%
ECHO Web-Net-Ext=%Web-Net-Ext%
ECHO Web-ISAPI-Ext=%Web-ISAPI-Ext%
ECHO Web-ISAPI-Filter=%Web-ISAPI-Filter%
ECHO Web-Windows-Auth=%Web-Windows-Auth%
ECHO Web-Filtering=%Web-Filtering%
ECHO Web-Mgmt-Console=%Web-Mgmt-Console%
ECHO WMF3=%WMF3%

ping 127.0.0.1 -n 5 >NUL

IF /I '%DOTNET%' EQU '1' (
ECHO Installing .NetFramework 4.0...
"PreRequisites\dotNetFx40_Full_x86_x64.exe" /Q /norestart
)

IF /I '%MSVC2010x86%' EQU '1' (
ECHO Installing Microsoft Visual C++ 2010 SP1 Redistributable Package (x86)
"PreRequisites\vcredist_x86.exe"  /passive /norestart
)

IF /I '%MSVC2010x64%' EQU '1' (
ECHO Installing Microsoft Visual C++ 2010 SP1 Redistributable Package (x64)
"PreRequisites\vcredist_x64.exe"  /passive /norestart
)

:: ===========================================================================================================
:: Web Server + required features
:: ===========================================================================================================
        >InstallIIS.ps1 ECHO.
        >>InstallIIS.ps1 ECHO Import-Module servermanager

IF /I '%Web-Server%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Server
)
IF /I '%Web-Static-Content%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Static-Content
)
IF /I '%Web-Default-Doc%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Default-Doc
)
IF /I '%Web-Asp-Net%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Asp-Net
)
IF /I '%Web-Net-Ext%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Net-Ext
)
IF /I '%Web-ISAPI-Ext%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-ISAPI-Ext
)
IF /I '%Web-ISAPI-Filter%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-ISAPI-Filter
)
IF /I '%Web-Windows-Auth%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Windows-Auth
)
IF /I '%Web-Filtering%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Filtering
)
IF /I '%Web-Mgmt-Console%' EQU '1' (
        >>InstallIIS.ps1 ECHO Add-WindowsFeature Web-Mgmt-Console
)

ECHO Installing Web Server and required features...
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\InstallIIS.ps1
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
        DEL /F /Q InstallIIS.ps1 >NUL

ECHO Installing Windows Management Framework 3.0...
IF /I '%WMF3%' EQU '1' (
c:\windows\system32\wusa.exe "PreRequisites\Windows6.1-KB2506143-x64.msu" /quiet /norestart
)

ECHO Installing Silverlight x64...
"PreRequisites\Silverlight_x64.exe" /q /doNotRequireDRMPrompt /ignorewarnings

ECHO Prepping AppV Server install post-reboot...
ECHO.
ECHO You may have to login to this server with a local admin account
ECHO to execute the install...

cd /d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
        >startup.cmd ECHO @ECHO OFF
        >>startup.cmd ECHO c:\windows\microsoft.net\framework\v4.0.30319\aspnet_regiis.exe -ir
        >>startup.cmd ECHO c:\windows\microsoft.net\framework64\v4.0.30319\aspnet_regiis.exe -ir
        >>startup.cmd ECHO net use Z: "\\FILESERVER\ctx_images_bdc\_ISO\AppV\App-V for RDS v5.0 SP1\APP-V 5.0 SERVER SP1"
        >>startup.cmd ECHO Z:
        >>startup.cmd ECHO ECHO Installing Management Server...
        >>startup.cmd ECHO APPV_SERVER_SETUP.EXE /QUIET  /AcceptEULA /MANAGEMENT_SERVER /MANAGEMENT_ADMINACCOUNT="DOMAIN\APV.Admins" /MANAGEMENT_WEBSITE_NAME="Microsoft App-V Management Service" /MANAGEMENT_WEBSITE_PORT="12345" /EXISTING_MANAGEMENT_DB_REMOTE_SQL_SERVER_NAME="WSSQL" /EXISTING_MANAGEMENT_DB_CUSTOM_SQLINSTANCE="PRDINST01" /EXISTING_MANAGEMENT_DB_NAME="APPV5DB"
        >>startup.cmd ECHO ECHO Installing Publishing Server...
        >>startup.cmd ECHO APPV_SERVER_SETUP.EXE /QUIET /AcceptEULA /PUBLISHING_SERVER /PUBLISHING_MGT_SERVER="http://%COMPUTERNAME%.%USERDNSDOMAIN%:12345" /PUBLISHING_WEBSITE_NAME="Microsoft App-V Publishing Service" /PUBLISHING_WEBSITE_PORT="55555"
        >>startup.cmd ECHO cd /d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
        >>startup.cmd ECHO START shutdown.exe -r -t 60 -f
        >>startup.cmd ECHO DEL /F /Q startup.cmd

ECHO Rebooting...
shutdown -r -t 60 -f
```

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->