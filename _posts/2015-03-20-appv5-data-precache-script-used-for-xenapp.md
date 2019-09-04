---
id: 568
title: 'AppV5 - Data Precache script (used for &)'
date: 2015-03-20T15:42:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/appv5-data-precache-script-used-for-&/
permalink: /2015/03/20/appv5-data-precache-script-used-for-&/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/appv5-data-precache-script-used-for.html
blogger_internal:
  - /feeds/7977773/posts/default/6705756987191535021
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Citrix
  - Provisioning Services
  - scripting
  - Target Device
---
This is the AppV5 Data Precache script we use to load our AppV5 packages on our Citrix & servers. � Because we use PVS and store the AppV package installation root folder on a persistent disk, we sometimes encounter issues that require us to 'clean up' the package installation root folder before the AppV5 packages will load. � What this script attempts to do is setup AppV5 as if it's a new install, grab any packages from the publishing server, then, if sharedcontentstore is disabled, fully mount/load the packages.

AppV5DataPrecache.cmd

<pre class="lang:batch decode:true ">:: ===========================================================================================================
::
:: Created by:      Saman Salehian
::          Intel Server Team
::          IBM Canada Ltd.
::
:: Creation Date:   Oct 16, 2012
:: Modified Date:   Nov 20, 2013 -- Saman Salehian & Trentent Tye - Added load all applications sequentially - App-V 5
::
:: File Name:       AppV5_Data_PreCache.cmd
::
:: Description:     Pre-Cache App-V Applications/Packages on & Server
:: Updates:             Updated to remove the 15min timeout and added a cleanup to remove stale AppV5 packages
:: Updates:             Feb 05, 2015 -- TTYE - delete "C:\ProgramData\Microsoft\AppV\Client" added to prevent vDisk from caching faulty junctions
::
:: ===========================================================================================================
 
@ECHO OFF
CLS
 
::Check to see if BLD server.  If it is, exit the AppV5 precache script
ECHO %COMPUTERNAME% | FINDSTR /I /C:"BLD"
IF [%ERRORLEVEL%] EQU [0] EXIT
 
IF NOT EXIST "C:\Program Files\Microsoft Application Virtualization\Client\AppVClient.exe" EXIT
 
:: ================================================================================
:: App-V 5 - Set server into offline workergroup
:: ================================================================================
 
::check for 'T' at the end of the computer name.  If 't' exists, set ZDC to test
SET ZDC=CTX301
IF /I [%COMPUTERNAME:~-1,1%] EQU [T] SET ZDC=CTX301T
 
 >%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO.
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO if ( (Get-PSSnapin -Name Citrix.&.Commands -ErrorAction SilentlyContinue) -eq $null )
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO {
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   Add-PSSnapin Citrix.&.Commands
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   $offline = @()
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   $offlineNames = Get-XAWorkerGroup "Offline" -Computername %ZDC%
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   $offline += $offlineNames.ServerNames
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   $offline += $env:computername
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO   Set-XAWorkerGroup "Offline" -ServerNames $offline -Computername %ZDC%
>>%WINDIR%\Temp\Set-Server-Offline.ps1 ECHO }
"%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"   -ExecutionPolicy bypass -file "%WINDIR%\Temp\Set-Server-Offline.ps1"
 
PING LOCALHOST -n 120 >NUL
 
gpupdate /target:computer /force
:: Disable Logons
reg.exe add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v WinStationsDisabled /t REG_SZ /d 1 /f
 
:: ================================================================================
:: App-V 5 - Set a loop with a break point so this script will retry itself only
::           5 times.
:: ================================================================================
 
IF '%1' GEQ '5' EXIT
IF '%1' EQU '' SET COUNT=0
SET /A COUNT=%1+1
 
 
:START
EVENTCREATE /ID 3 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "Starting AppV5_Data_PreCache.cmd %COUNT%"
 
 
 
:: ================================================================================
:: App-V 5 - Check if folders still exist in registry, if so create them then
::           try removing them again
:: ================================================================================
net start appvclient
FOR /F "tokens=1-5" %%A IN ('sc query appvclient ^| findstr /i /c:"STATE"') DO IF /I '%%D' EQU 'STOPPED' (
for /f "tokens=1-6" %%a IN ('reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages /s ^| findstr /i /c:"PackageRoot"') DO mkdir %%c
)
net start appvclient
 
 
 
:: ================================================================================
:: App-V 5 - Remove old packages
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
    >Appv5ClientRefresh-01.ps1 ECHO.
    >>Appv5ClientRefresh-01.ps1 ECHO Import-Module AppVClient
    >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientConnectionGroup *
    >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientPackage *
    >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientConnectionGroup *
    >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientPackage *
    >>Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientConnectionGroup *
    >>Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientPackage *
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass -File .\Appv5ClientRefresh-01.ps1
    rmdir /s /q "C:\ProgramData\Microsoft\AppV\Client"
    DEL /F /Q Appv5ClientRefresh-01.ps1 >NUL
)
 
 
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Remove old packages, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - Stop Service
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        net stop AppVClient
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Stop Service, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - Remove Directory PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        rmdir /s /q D:\AppVData\PackageInstallationRoot
)
 
mkdir D:\AppVData\PackageInstallationRoot
ping 127.0.0.1 >NUL
 
:: ================================================================================
:: App-V 5 - Shorten package paths to hopefully take ownership/delete them
:: ================================================================================
D:
cd /d D:\AppVData\PackageInstallationRoot
IF NOT EXIST D:\AppVData\PackageInstallationRoot GOTO SKIPPATH
        ECHO. >%TEMP%\shortenPath.ps1
        ECHO for ($i=1; $i -le 40; $i++) { >>%TEMP%\shortenPath.ps1 
        ECHO gci ^| rename-item -newname { [string]($_.name).substring(1) } >>%TEMP%\shortenPath.ps1 
        ECHO } >>%TEMP%\shortenPath.ps1 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass -file "%TEMP%\shortenPath.ps1"
 
::Shorten all version paths as well...
        for /f %%A IN ('dir /b') do (
             cd D:\AppVData\PackageInstallationRoot\%%A
             "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass -file "%TEMP%\shortenPath.ps1"
        )
        cd /d D:\AppVData
        del /f /q "%TEMP%\shortenPath.ps1"
 
:SKIPPATH
 
cd /d D:\AppVData
 
:: ================================================================================
:: App-V 5 - Remove Directory PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        rmdir /s /q D:\AppVData\PackageInstallationRoot
)
 
 
:: ================================================================================
:: App-V 5 - Take Ownership of PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        takeown /F D:\AppVData\PackageInstallationRoot /R /A >NUL
        for /f "tokens=*" %%A IN ('dir /b /s D:\AppVData\PackageInstallationRoot') do takeown /F "%%A"
        for /f "tokens=*" %%A IN ('dir /b /s D:\AppVData\PackageInstallationRoot') do takeown /F "%%A" /A
        for /f "tokens=*" %%A IN ('dir /b /s D:\AppVData\PackageInstallationRoot') do icacls %%A /T /grant Administrators:F
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Take Ownership of PackageInstallationRoot, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - Grant Control Admins PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        icacls D:\AppVData\PackageInstallationRoot /T /grant Administrators:F >NUL
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Grant Control Admins PackageInstallationRoot, ERRORLEVEL %ERRORLEVEL%"
 
         
:: ================================================================================
:: App-V 5 - Remove Directory PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        rmdir /s /q D:\AppVData\PackageInstallationRoot
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Remove Directory PackageInstallationRoot, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - Take Ownership of PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        takeown /F D:\AppVData\PackageInstallationRoot /R >NUL
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Take Ownership of PackageInstallationRoot 2, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - Grant Control Admins PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        icacls D:\AppVData\PackageInstallationRoot /T /grant Administrators:F >NUL
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Grant Control Admins PackageInstallationRoot 2, ERRORLEVEL %ERRORLEVEL%"
 
         
:: ================================================================================
:: App-V 5 - Remove Directory PackageInstallationRoot
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        rmdir /s /q D:\AppVData\PackageInstallationRoot
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Remove Directory PackageInstallationRoot 2, ERRORLEVEL %ERRORLEVEL%"
 
 
:: ================================================================================
:: App-V 5 - Start Service
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        net start AppVClient
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Start Service, ERRORLEVEL %ERRORLEVEL%"
 
:: ================================================================================
:: App-V 5 - MS provided script to try and ensure we have a clean system.
:: ================================================================================
"%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass  -File "C:\Program Files\Microsoft Application Virtualization\Client\Disable-AppVClient.ps1"  -ModulePath "C:\Program Files\Microsoft Application Virtualization\Client\AppvClient\AppvClient.psd1" -RemoveAllPackages -PackageInstallationRoot "D:\AppVData\PackageInstallationRoot"
 
 
:: ================================================================================
:: App-V 5 - Refresh Published applications
:: ================================================================================
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        schtasks /run /TN \Microsoft\AppV\Publishing\1_global_periodic
)
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Refresh Published applications, ERRORLEVEL %ERRORLEVEL%"
 
 
 
:: ================================================================================
:: App-V 5 - If packages fail to load, try this script again...
:: Note: the following ps1 script will fail if run from %temp% as the user profile 
:: sometimes closes, deleting the path for the script, before the script is run.
:: running it from %windir%\Temp works as it will always be there.
:: ================================================================================
:RETRYMOUNT
ping 127.0.0.1 -n 60 >NUL
 
 
EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Checking packages..."
 
    >%WINDIR%\Temp\check-packages.ps1 ECHO.
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Write-EventLog –LogName SYSTEM –Source "AppV5_Data_PreCache.cmd" –EntryType Information –EventID 4 –Message ("Starting Check Packages Script")
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Import-Module AppVClient
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvPublishingServer ^| Sync-AppvPublishingServer
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvClientConnectionGroup
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvClientPackage
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $apps = Get-AppvClientPackage
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $SCSMode = Get-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\AppV\Client\Streaming  -Name "SharedContentStoreMode"
    >>%WINDIR%\Temp\check-packages.ps1 ECHO if ($SCSMode.SharedContentStoreMode -eq 0) { $apps ^| Mount-AppvClientPackage }
    >>%WINDIR%\Temp\check-packages.ps1 ECHO sleep 60
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvPublishingServer ^| Sync-AppvPublishingServer
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvClientConnectionGroup
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Get-AppvClientPackage
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $apps ^| Add-AppvClientPackage
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $apps ^| Publish-AppvClientPackage -Global
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $apps = Get-AppvClientPackage
    >>%WINDIR%\Temp\check-packages.ps1 ECHO if ($SCSMode.SharedContentStoreMode -eq 0) { $apps ^| Mount-AppvClientPackage }
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $string = $apps ^| Select-Object Name,PercentLoaded ^| out-string
    >>%WINDIR%\Temp\check-packages.ps1 ECHO $string += $SCSMode ^| Select-Object SharedContentStoreMode ^| out-string
    >>%WINDIR%\Temp\check-packages.ps1 ECHO Write-EventLog –LogName SYSTEM –Source "AppV5_Data_PreCache.cmd" –EntryType Information –EventID 4 –Message ($string)
    >>%WINDIR%\Temp\check-packages.ps1 ECHO if ($apps.Count -eq 0) { Start-Process -FilePath "C:\swinst\AppV_Data_PreCache\AppV5_Data_PreCache.cmd" %COUNT% }
        ping 127.0.0.1 -n 5 >NUL
        IF EXIST "%WINDIR%\Temp\check-packages.ps1" EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Checking packages ps1 exists"
 
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" -ExecutionPolicy bypass -file "%WINDIR%\Temp\check-packages.ps1"
        EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Check Packages Script, ERRORLEVEL %ERROR%"
 
     >%WINDIR%\Temp\Set-Server-Online.ps1 ECHO.
    >>%WINDIR%\Temp\Set-Server-Online.ps1 ECHO if ( (Get-PSSnapin -Name Citrix.&.Commands -ErrorAction SilentlyContinue) -eq $null )
    >>%WINDIR%\Temp\Set-Server-Online.ps1 ECHO {
    >>%WINDIR%\Temp\Set-Server-Online.ps1 ECHO   Add-PsSnapin Citrix.&.Commands
    >>%WINDIR%\Temp\Set-Server-Online.ps1 ECHO   Remove-XAWorkerGroupServer -WorkerGroupName "Offline" -ServerNames $env:computername -Computername %ZDC%
    >>%WINDIR%\Temp\Set-Server-Online.ps1 ECHO }
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass -file "%WINDIR%\Temp\Set-Server-Online.ps1"
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
 
        pushd %~dp0
        EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Start RegistryStaging"
    "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" -ExecutionPolicy bypass -file "C:\swinst\AppV_Data_PreCache\Appv5RegistryStaging.ps1"
        EVENTCREATE /ID 2 /L SYSTEM /T INFORMATION /SO "AppV5_Data_PreCache.cmd" /D "App-V 5 - Complete RegistryStaging"
        pop %~dp0
 
 
ping 127.0.0.1 -n 60 >NUL
 
gpupdate /target:computer /force
:: Disable Logons
reg.exe add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v WinStationsDisabled /t REG_SZ /d 1 /f</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->