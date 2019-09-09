---
id: 573
title: Citrix PVS Target Device Update script
date: 2015-03-20T14:31:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/citrix-pvs-target-device-update-script/
permalink: /2015/03/20/citrix-pvs-target-device-update-script/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/citrix-pvs-target-device-update-script.html
blogger_internal:
  - /feeds/7977773/posts/default/4901103717259338735
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - scripting
  - Target Device

---
Saman and I have created a Citrix PVS update script we use and run each time we update a PVS target device.  This script has been generated after finding numerous issues that needed to be fixed after updating a PVS image.  It includes fixes to issues with AppV5, PVS, & 6.5, etc.

We also made the script to be able to be run automatically by accepting command-line parameters so that we can use it to do Windows Updates with PVS's automatic vDisk update feature.

I've removed email addresses and some specific application fixes that would only be relevant to our environment.  So I've tried to generalize this the best I can.  We tried to make this script dynamic so if you have a vDisk without AppV it will skip the features that require AppV, skip 64bit only features, etc.

I will post the supplemental scripts in more posts.

<pre class="lang:batch decode:true ">::================================================================================
::
:: Created by:  Saman Salehian
::   Intel Server Team
::   IBM Canada Ltd.
::
:: Creation Date: Nov 09, 2012
:: Modified Date: Feb 05, 2013 -- TTYE - added 64bit appv state 0 registry key
:: Modified Date: Feb 13, 2013 -- Saman Salehian - added command to disconnect mapped drives
:: Modified Date: Feb 25, 2013 -- TTYE - added command to clear AppV 5 packages
:: Modified Date: Mar 28, 2013 -- TTYE - Commented out netsh command to prevent network loss
:: Modified Date: May 02, 2013 -- TTYE - Added ability to parse for passed variables
:: Modified Date: May 02, 2013 -- TTYE - Stopped all scheduled tasks
:: Modified Date: May 21, 2013 -- Saman Salehian - added cleanup script for non present devices
:: Modified Date: May 21, 2013 -- Saman Salehian - added option to do not apply Windows Updates
:: Modified Date: May 21, 2013 -- Saman Salehian - added delete security database to fix GPO issues
:: Modified Date: Jul 04, 2013 -- TTYE - added powershell script to remove provisioning dynamic DNS registration
:: Modified Date: Jul 23, 2013 -- Saman Salehian - commented out delete security database to fix GPO issues
:: Modified Date: Sep 24, 2014 -- Saman Salehian - added logic to check if AppV is installed or skip AppV portion
:: Modified Date: Dec 19, 2014 -- Saman Salehian - added logic to check if Meditech exists then update Meditech MagicCS and FS Clients
:: Modified Date: Dec 31, 2014 -- Saman Salehian - added logic to check if Millennium exists then install the full client automatically
:: Modified Date: Feb 05, 2015 -- TTYE - delete "C:\ProgramData\Microsoft\AppV\Client" added to prevent vDisk from caching faulty junctions
:: Modified Date: Feb 06, 2015 -- TTYE - Added command line to update virus def's
:: Modified Date: Mar 14, 2015 -- Saman Salehian - added logic to clear Citrix policies history per CTX134961 (& Policies not Applying Correctly)
::
::
:: File Name:  PVS_Target_Device_Update-v3.0.cmd
::
:: Description:  Automated Process for Updating PVS vDisk
::
::================================================================================
 
 
@TITLE PVS Target Device Update Script
@ECHO OFF
CLS
COLOR 1E
 
 
:: ===========================================================================================================
:: Stops all scheduled tasks
:: ===========================================================================================================
FOR /f "delims=," %%A IN ('schtasks /query /fo csv ^| findstr /v /i /c:"Folder"') DO schtasks /end /tn %%A
CLS
 
 
:: ===========================================================================================================
:: See if there is any parameters passed and set variables accordingly
:: 1) Pull string off the command line
:: 2) filter out " and '
:: 3) break each set out by spaces
:: 4) save to temp file
:: 5) iterate through temp file (for) and create variables.
:: ===========================================================================================================
 
 
:: Example of acceptable parameters are: SHUTDOWN=1 vDISK=1
:: Parameters are:
:: SHUTDOWN=1 - Reboot
:: SHUTDOWN=2 - Shutdown
:: SHUTDOWN=3 - Leave system online
:: VDISK=1 - Do not apply Windows Updates
:: VDISK=2 - Test vDisk
:: VDISK=3 - Prod vDisk
:: 
:: Example of command:
:: RunMe-vDiskAutoUpdate.cmd SHUTDOWN=1 VDISK=3
:: Sets the script to reboot and the vDisk is Prod
:: RunMe-vDiskAutoUpdate.cmd SHUTDOWN=2 VDISK=2
:: Sets the script to shutdown and the vDisk is Test
:: RunMe-vDiskAutoUpdate.cmd SHUTDOWN=3 VDISK=3
:: Sets the script to leave the system online and the vDisk is Prod
:: 
 
ECHO PARAMETERS "%*"
SET PARAMS=%*
 
:: NOTE: We are replacing quotations with whitespace characters!  Ensure there is no
:: whitespace in the parameters, but whitespace *between* parameters.  Filtering out the
:: quotations ensures we can call this from the command line without issue.
 
IF "%PARAMS%"=="" GOTO SKIPPARAMETERS
CALL SET PARAMS=%%PARAMS:"= %%
CALL SET PARAMS=%%PARAMS:'= %%
 
:: Break each set of parameters into its own set, seperated by the equals sign.
:: Once we iterate through all the parameters we "breakout"
 
FOR /f "tokens=1-26 delims= " %%A IN ("%PARAMS%") DO (
IF '%%A' EQU '' GOTO BREAKOUT
ECHO %%A > "%TEMP%\variables.txt"
IF '%%B' EQU '' GOTO BREAKOUT
ECHO %%B >> "%TEMP%\variables.txt"
)
:BREAKOUT
 
 
:: Now we iterate through the temp file and auto-create the variables
FOR /f "tokens=1-2 delims==" %%A IN ('TYPE "%TEMP%\variables.txt"') DO (
 IF /I '%%A' EQU 'SHUTDOWN' set SHUTDOWN=%%B
 IF /I '%%A' EQU 'VDISK' set VDISK=%%B
)
 
:SKIPPARAMETERS
 
 
:: ===========================================================================================================
:: Prompt for values - Reboot/Shutdown & Citrix Environment (Test or Prod)
:: ===========================================================================================================
 
SET M=%SHUTDOWN%
IF "%M%" NEQ "" GOTO SkipShutdownMenu
 
:SHUTDOWNMENU
ECHO.
ECHO Select Reboot or Shutdown:
ECHO 1 - Reboot
ECHO 2 - Shutdown
ECHO 3 - Leave system online
ECHO.
SET /P M=Type 1 or 2 then press ENTER:
:SkipShutdownMenu
IF %M%==1 (
 ECHO ..............................................................................
 ECHO Server will be rebooted after the updates and you need to run the script again to prepare vDisk for Standard Mode
 ECHO ..............................................................................
 SET ShutdownMode=REBOOTME
 GOTO WSUSMENU
)
IF %M%==2 (
 ECHO ..............................................................................
 ECHO Server will be shutdown after the updates. Then you can promote the vDisk version to Test/Production.
 ECHO ..............................................................................
 SET ShutdownMode=SHUTDOWNME
 GOTO WSUSMENU
)
IF %M%==3 (
 ECHO ..............................................................................
 ECHO Server will be left online.  This is required for vDisk Auto-Update Management 
 ECHO ..............................................................................
 SET ShutdownMode=ONLINE
 GOTO WSUSMENU
)
 
GOTO SHUTDOWNMENU
 
 
 
:WSUSMENU
 
SET M=%VDISK%
IF "%M%" NEQ "" GOTO SkipWSUSMenu
 
ECHO.
ECHO Select type of vDisk:
ECHO 1 - Do NOT apply Windows Updates
ECHO 2 - Test vDisk
ECHO 3 - Prod vDisk
ECHO.
SET /P M=Type 1, 2 or 3 then press ENTER:
:SkipWSUSMenu
 
IF %M%==1 (
 ECHO ..............................................................................
 ECHO Windows updates will not apply to vDisk.
 ECHO ..............................................................................
 SET WSUSComputerGroup=NONE
 GOTO VariableIsSet
)
IF %M%==2 (
 ECHO ..............................................................................
 ECHO All the patches approved for Day01.2300.WS computer group will be applied to this server.
 ECHO ..............................................................................
 SET WSUSComputerGroup=Day01.2300.WS
 GOTO VariableIsSet
)
IF %M%==3 (
 ECHO ..............................................................................
 ECHO All the patches approved for Day13.2300.WS computer group will be applied to this server.
 ECHO ..............................................................................
 SET WSUSComputerGroup=Day13.2300.WS
 GOTO VariableIsSet
)
ECHO.
GOTO WSUSMENU
 
 
 
 
:VariableIsSet
 
 
:: Continue to run the script
PUSHD "%~dp0"
ECHO 60 seconds delay to allow services to start...
ping 127.0.0.1 -n 60 >NUL
 
:: ================================================================================
:: Reset Computer Account
:: ================================================================================
ECHO Reset Computer Account
NETDOM RESET %COMPUTERNAME% /DOMAIN:healthy.bewell.ca 
PING LOCALHOST -n 5 >NUL
 
:: ================================================================================
:: Windows Time Service Resync
:: ================================================================================
ECHO Windows Time Service Resync
NET START w32time
W32TM /CONFIG /UPDATE 
W32TM /RESYNC 
PING LOCALHOST -n 5 >NUL
 
:: ================================================================================
:: Delete Published Applications Folder
:: ================================================================================
IF EXIST C:\PublishedApplications (
 ECHO Delete Published Applications Folder
 ATTRIB -R C:\PublishedApplications\*.* /S
 DEL /S /Q C:\PublishedApplications\*.*
)
 
:: ================================================================================
:: Refresh Group Policies
:: ================================================================================
ECHO Refresh Group Policies
 
ECHO N | GPUPDATE /FORCE 
PING LOCALHOST -n 5 >NUL
 
 
:: ================================================================================
:: Configure WinRM for Desktop Director
:: ================================================================================
IF /I [%computername:~-1,1%] EQU [T] (
 ECHO Configuring WinRM for TEST
 ConfigRemoteMgmt.exe /configwinrmuser HEALTHY\AHS.CTX.Users.DesktopDirector.TST
) ELSE (
 ECHO Configuring WinRM for PROD
 ConfigRemoteMgmt.exe /configwinrmuser HEALTHY\AHS.CTX.Users.DesktopDirector.PRD
)
 
:: ===========================================================================================================
:: Remove Non-present Hareware
:: ===========================================================================================================
CD RemoveNonPresentHardware >NUL
CALL "1_pre-get_partition_and_netinfo.cmd"
CALL "2_post-remove_nonpresent_hardware_v33.cmd"
RMDIR /S /Q ""%SYSTEMDRIVE%\Admin"
CD.. >NUL
PING -n 5 LOCALHOST >NUL
 
 
:: ===========================================================================================================
:: Update PSTools and Wireshark
:: ===========================================================================================================
CD Update_Swinst_Tools >NUL
"%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy Unrestricted -File "Update_Tools.ps1"
CD.. >NUL
PING -n 5 LOCALHOST >NUL
 
 
:: ================================================================================
:: Apply Windows Updates
:: ================================================================================
ECHO Apply Windows Updates
IF NOT %WSUSComputerGroup%==NONE (
 ECHO Apply Windows Updates
 CALL Windows_Update.cmd "emailMe@me.ca" %WSUSComputerGroup%
 PING LOCALHOST -n 5 >NUL
)
TYPE "%TEMP%\vbswsus-status.log"
 
 
:: ================================================================================
:: Clear App-V Data & Reset the Client
:: ================================================================================
IF NOT EXIST "%ProgramFiles%\Microsoft Application Virtualization" (
 IF NOT EXIST "%ProgramFiles(x86)%\Microsoft Application Virtualization Client" (
  GOTO SKIPAPPVALL
 )
)
 
ECHO Clear App-V Data ^& Reset the Client
NET START sftlist
TASKLIST | FINDSTR /i /c:"sftlist"
IF "%ERRORLEVEL%" equ "1" GOTO SKIPAPPV4
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 "%ProgramFiles(x86)%\Microsoft Application Virtualization Client\sftmime.exe" remove obj:app /global /complete
) ELSE (
 "%ProgramFiles%\Microsoft Application Virtualization Client\sftmime.exe" remove obj:app /global /complete  
)
 
:SKIPAPPV4
 
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 REG ADD "HKLM\SOFTWARE\Wow6432Node\Microsoft\SoftGrid\4.5\Client\AppFS" /V State /T REG_DWORD /D 0 /F 
) ELSE (
 REG ADD "HKLM\SOFTWARE\Microsoft\SoftGrid\4.5\Client\AppFS" /V State /T REG_DWORD /D 0 /F       
)
 
ECHO Clearing AppV 5 Packages...
ECHO Checking for folders that don't exist that will prevent AppV from loading packages correctly...
net start appvclient
FOR /F "tokens=1-5" %%A IN ('sc query appvclient ^| findstr /i /c:"STATE"') DO IF /I '%%D' EQU 'STOPPED' (
for /f "tokens=1-6" %%a IN ('reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages /s ^| findstr /i /c:"PackageRoot"') DO mkdir %%c
)
 
 
IF EXIST "C:\Program Files\Microsoft Application Virtualization\Client\AppVClient.exe" (
 >Appv5ClientRefresh-01.ps1 ECHO.
 >>Appv5ClientRefresh-01.ps1 ECHO Import-Module AppVClient
 >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientConnectionGroup * -Global
 >>Appv5ClientRefresh-01.ps1 ECHO Stop-AppvClientPackage * -Global
 >>Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientConnectionGroup *
 >>Appv5ClientRefresh-01.ps1 ECHO Remove-AppvClientPackage *
 >>Appv5ClientRefresh-01.ps1 ECHO Get-AppvClientConnectionGroup -all ^| Remove-AppvClientConnectionGroup
 >>Appv5ClientRefresh-01.ps1 ECHO Get-AppvClientPackage -all ^| Remove-AppvClientPackage
 >>Appv5ClientRefresh-01.ps1 ECHO Get-AppvClientPackage -all *
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\Appv5ClientRefresh-01.ps1
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"  -ExecutionPolicy bypass -File "C:\Program Files\Microsoft Application Virtualization\Client\Disable-AppVClient.ps1"  -ModulePath "C:\Program Files\Microsoft Application Virtualization\Client\AppvClient\AppvClient.psd1" -RemoveAllPackages -PackageInstallationRoot "D:\AppVData\PackageInstallationRoot"  
        ping 127.0.0.1 -n 60 >NUL
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
 DEL /F /Q Appv5ClientRefresh-01.ps1 >NUL
        net stop AppVClient
        takeown /F D:\AppVData\PackageInstallationRoot /R /A >NUL
        icacls D:\AppVData\PackageInstallationRoot /T /grant Administrators:F >NUL
        rmdir /s /q D:\AppVData\PackageInstallationRoot
        takeown /F D:\AppVData\PackageInstallationRoot /R >NUL
        icacls D:\AppVData\PackageInstallationRoot /T /grant Administrators:F >NUL
        rmdir /s /q D:\AppVData\PackageInstallationRoot
        rmdir /s /q "C:\ProgramData\Microsoft\AppV\Client"
)
 
ECHO Post AppV5 package check...
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Packages
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Integration\Packages
 
reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Packages /f
reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages /f
reg delete HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Integration\Packages /f
 
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Packages
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Integration\Packages
 
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Packages
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages
reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Integration\Packages
 
ECHO D:\AppVData\PackageInstallationRoot
dir /b D:\AppVData\PackageInstallationRoot
TASKKILL /IM powershell.exe /F
"%SystemRoot%\System32\RUNDLL32.EXE" user32.dll, UpdatePerUserSystemParameters
 
ECHO AppV 5 - Fix up any additional folders so AppV will start.
FOR /F "tokens=1-5" %%A IN ('reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages /s ^| findstr /i /c:"packageroot"') DO mkdir %%C
 
PING LOCALHOST -n 5 >NUL
 
IF EXIST "C:\Program Files\Microsoft Application Virtualization\Client\AppVClient.exe" (
        ECHO Disable BackgroundRegistry Processing...
        REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Subsystem" /V NoBackgroundRegistryStaging /T REG_DWORD /D 0x0 /F
        copy /y "\\citrixnas01\ctx_images_bdc\_Scripts\AppVDataPreCache\AppV5_Data_PreCache.cmd" "C:\swinst\AppV_Data_PreCache\AppV5_Data_PreCache.cmd"
        copy /y "\\citrixnas01\ctx_images_bdc\_Scripts\AppVDataPreCache\Appv5RegistryStaging.ps1" "C:\swinst\AppV_Data_PreCache\Appv5RegistryStaging.ps1"
        del /q "C:\swinst\AppV_Data_PreCache\RegistryStagingTime.csv"
)
 
:SKIPAPPVALL
 
:: ================================================================================
:: Disable Dynamic DNS Registration on Provisioning NIC
:: ================================================================================
ECHO Disabling Dynamic DNS Registration on Prov NIC
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
        START "" "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" "%~dp0disableddnsregistration.ps1"
        ping 127.0.0.1 -n 60 >NUL
        "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
)
TASKKILL /im powershell.exe /f
PING LOCALHOST -n 5 >NUL
 
:: ================================================================================
:: Sets the interface metrics - production 1, provisioning 2
:: ================================================================================
ECHO Setting Interface Metrics...
netsh int ipv4 set interface "Production" metric=1
netsh int ipv4 set interface "Provision" metric=2
 
 
:: ================================================================================
:: Set TCPAckFrequency and TCPNoDelay, this is to improve MetaVision Performance
:: ================================================================================
ECHO Settings TCPAckFrequency and TCPNoDelay
FOR /f "tokens=1" %%A IN ('reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\Tcpip\Parameters\Interfaces') do reg add %%A /v TcpAckFrequency /t REG_DWORD /d 0x1 /f
FOR /f "tokens=1" %%A IN ('reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\Tcpip\Parameters\Interfaces') do reg add %%A /v TcpNoDelay /t REG_DWORD /d 0x1 /f
 
:: ================================================================================
:: Set SMB2 tuning properties http://support.citrix.com/article/CTX132799 http://download.microsoft.com/download/6/B/2/6B2EBD3A-302E-4553-AC00-9885BBF31E21/Perf-tun-srv-R2.docx
:: ================================================================================
ECHO Settings SMB2 tuning properties
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters" /v DisableBandwidthThrottling /t REG_DWORD /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters" /v DisableLargeMtu /t REG_DWORD /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters" /v FileInfoCacheEntriesMax /t REG_DWORD /d 65536 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters" /v DirectoryCacheEntriesMax /t REG_DWORD /d 4096 /f
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters" /v FileNotFoundCacheEntriesMax  /t REG_DWORD /d 65536 /f
 
:: ================================================================================
:: Improve performance of Citrix vDisks - CTX126042, CTX117374
:: ================================================================================
ECHO Improve performance of Citrix vDisks - CTX126042
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\BNIStack\Parameters" /v WcHDNoIntermediateBuffering /t REG_DWORD /d 2 /f
ECHO Improve performance of Citrix vDisks - CTX117374
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\TCPIP\Parameters" /v DisableTaskOffload /t REG_DWORD /d 1 /f
 
 
:: ================================================================================
:: Set EventLogs for 2008 servers to the D drive
:: ================================================================================
ECHO Setting EventLog Path to the D:\EventLogs drive...
 
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
 START "" "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" "%~dp0Move-Log-Files.ps1"
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
 
:: ================================================================================
:: Set EdgeSight Service to Manual and Clear EdgeSight Data
:: ================================================================================
ECHO Set EdgeSight Service to Manual and Clear EdgeSight Data
SC STOP RSCorSvc 
PING LOCALHOST -n 5 >NUL
IF EXIST "D:\EdgeSightData\EdgeSight.ini" (
 ECHO Y | DEL "D:\EdgeSightData\EdgeSight.ini" 
)
 
 
:: ================================================================================
:: Speed Up .Net Applications by resetting and generating performance optimization
:: http://blogs.msdn.com/b/dotnet/archive/2013/08/06/wondering-why-mscorsvw-exe-has-high-cpu-usage-you-can-speed-it-up.aspx
:: ================================================================================
ECHO Speed Up .Net Applications by resetting and generating performance optimization
START /WAIT cscript.exe //NOLOGO nGen.wsf
 
:: ================================================================================
:: Generalize PVS vDisk - Provisioning Services Device Optimizer
:: ================================================================================
ECHO Generalize PVS vDisk - Provisioning Services Device Optimizer
START "" "PVS.exe"
"%ProgramFiles%\Citrix\Provisioning Services\TargetOSOptimizer.exe"
PING LOCALHOST -n 5 >NUL
 
 
:: ================================================================================
:: Run &Prep for & 5 or Role Manager for & 6.5
:: ================================================================================
net START imaservice
FOR /F "TOKENS=2,*" %%a IN ('REG QUERY HKLM\SYSTEM\CurrentControlSet\Control\Citrix /v NewProductVersion') DO SET &Version=%%b
IF %&Version% EQU 4.6 (
 ECHO & 5 - Running &Prep
 ECHO.
 ECHO ================================================================
 IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
  "%ProgramFiles(x86)%\Citrix\&Prep\&Prep.exe" /Pvs /Xte
 ) ELSE (
  "%ProgramFiles%\Citrix\&Prep\&Prep.exe" /Pvs /Xte
 )
 ECHO ================================================================
)
 
IF %&Version% EQU 6.5.0 (
 ECHO & 6.5 - Running Role Manager
 ECHO.
 ECHO ================================================================
 "%ProgramFiles(x86)%\Citrix\&\ServerConfig\&ConfigConsole.exe" /ExecutionMode:ImagePrep /RemoveCurrentServer:True /ClearLocalDatabaseInformation:True
 ECHO ================================================================
)
PING LOCALHOST -n 5 >NUL
 
 
:: ================================================================================
:: Check registry after & Prep and fix IMA issues
:: ================================================================================
PING LOCALHOST -n 10 >NUL
 >FixIMA.ps1 ECHO.
 >>FixIMA.ps1 ECHO $inherit = [system.security.accesscontrol.InheritanceFlags]"ContainerInherit, ObjectInherit"
 >>FixIMA.ps1 ECHO $propagation = [system.security.accesscontrol.PropagationFlags]"None"
 >>FixIMA.ps1 ECHO $acl = Get-Acl HKLM:\SOFTWARE\Wow6432Node\Citrix\IMA\Status
 >>FixIMA.ps1 ECHO $rule = New-Object System.Security.AccessControl.RegistryAccessRule ("NT AUTHORITY\SYSTEM","FullControl",$inherit,$propagation,"Allow")
 >>FixIMA.ps1 ECHO $acl.SetAccessRule($rule)
 >>FixIMA.ps1 ECHO $rule = New-Object System.Security.AccessControl.RegistryAccessRule ("NT AUTHORITY\NETWORK SERVICE","FullControl",$inherit,$propagation,"Allow")
 >>FixIMA.ps1 ECHO $acl.SetAccessRule($rule)
 >>FixIMA.ps1 ECHO $rule = New-Object System.Security.AccessControl.RegistryAccessRule ("BUILTIN\Administrators","FullControl",$inherit,$propagation,"Allow")
 >>FixIMA.ps1 ECHO $acl.SetAccessRule($rule)
 >>FixIMA.ps1 ECHO $acl ^|Set-Acl -Path HKLM:\SOFTWARE\Wow6432Node\Citrix\IMA\Status
 
REG QUERY HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status
IF "%ERRORLEVEL%" EQU "1" (
    IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
        reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA /v DataSourceName /t REG_SZ /d "C:\Program Files (x86)\Citrix\Independent Management Architecture\mf20.dsn" /f
        reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status /v EnhancedDesktopExperienceConfigured /t REG_DWORD /d 0x1 /f
        reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status /v Provisioned /t REG_DWORD /d 0x1 /f
        reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status /v Ready /t REG_DWORD /d 0x1 /f
        reg add HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status /v Joined /t REG_DWORD /d 0x0 /f
 
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Unrestricted
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" .\FixIMA.ps1
 "%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe" Set-ExecutionPolicy Restricted
 
        TASKKILL /im mfcom.exe /f
        NET STOP imaservice
        DSMAINT recreatelhc
    )
)
DEL /Q .\FixIMA.ps1
 
:: ================================================================================
:: Update Symantec Client Virus Definitions
:: ================================================================================
ECHO Stop Symantec Management Client
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 "%ProgramFiles(x86)%\Symantec\LiveUpdate\LUALL.EXE" -s
) ELSE (
 "%ProgramFiles%\Symantec\LiveUpdate\LUALL.EXE" -s
)
 
:: ================================================================================
:: Stop Symantec Management Client
:: ================================================================================
ECHO Stop Symantec Management Client
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 "%ProgramFiles(x86)%\Symantec\Symantec Endpoint Protection\smc.exe" -stop 
) ELSE (
 "%ProgramFiles%\Symantec\Symantec Endpoint Protection\smc.exe" -stop 
)
 
FOR /F "TOKENS=2,*" %%a IN ('REG QUERY "HKLM\SOFTWARE\Symantec\Symantec Endpoint Protection\SMC" /v Version') DO SET SEPClientVersion=%%b
IF %SEPClientVersion% EQU 11.0 (
 REG ADD "HKLM\SYSTEM\CurrentControlSet\Services\SmcService" /V Start /T REG_DWORD /D 3 /F 
)
IF %SEPClientVersion% EQU 12.1 (
 REG ADD "HKLM\SYSTEM\CurrentControlSet\Services\SepMasterService" /V Start /T REG_DWORD /D 3 /F 
)
 
PING LOCALHOST -n 5 >NUL
 
:: ================================================================================
:: Delete User Profiles
:: ================================================================================
ECHO Delete User Profiles
CSCRIPT //NOLOGO Delete_User_Profiles.vbs /C 
PING LOCALHOST -n 5 >NUL
 
:: ================================================================================
:: Clear Networking Related Caches (DNS and ARP)
:: ================================================================================
 ECHO Clear Networking Related Caches (DNS and ARP)
IPCONFIG /FLUSHDNS 
ARP -D 
::NETSH WINSOCK RESET >NUL
 
:: ================================================================================
:: Clear Temp Folders
:: ================================================================================
ECHO Clear Temp Folders
ECHO Y | DEL "%TEMP%\." 
ECHO Y | DEL "%SYSTEMROOT%\TEMP\." 
RMDIR /S /Q "%SYSTEMDRIVE%\SWINST\BGINFO"
PING LOCALHOST -n 5 >NUL
 
 
:: ================================================================================
:: Run Disk Cleanup
:: ================================================================================
ECHO Running Disk Cleanup...
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 cleanmgr.exe /sagerun:10
)
 
:: ================================================================================
:: Delete Server Health Check Logs From System Drive
:: ================================================================================
ECHO Delete Server Health Check Logs From System Drive
IF EXIST "%SYSTEMDRIVE%\swinst\Server Health Check" (
 RMDIR /Q /S "%SYSTEMDRIVE%\swinst\Server Health Check"
 PING LOCALHOST -n 5 >NUL
)
 
:: ================================================================================
:: Clear Event Logs
:: ================================================================================
ECHO Clear Event Logs
CSCRIPT //NOLOGO Clear_Event_Logs.vbs 
PING LOCALHOST -n 5 >NUL
 
POPD
 
:: ================================================================================
:: Delete Group Policy History - CTX128749
:: ================================================================================
ECHO Deleting Group Policy History
IF EXIST %SYSTEMROOT%\SysWOW64\notepad.exe (
 RD /S /Q "C:\ProgramData\Microsoft\Group Policy\history"
)
 
:: ================================================================================
:: CTX134961 - & Policies not Applying Correctly
:: ================================================================================
ECHO Deleting Citrix Group Policy History
IF EXIST "C:\ProgramData\Citrix\GroupPolicy" (
 DEL /S /Q "C:\ProgramData\Citrix\GroupPolicy\*.*"
)
IF EXIST "C:\ProgramData\CitrixCseCache" (
 DEL /S /Q "C:\ProgramData\CitrixCseCache\*.*"
)
IF EXIST "C:\Windows\System32\GroupPolicy\Machine\Citrix\GroupPolicy" (
 DEL /S /Q "C:\Windows\System32\GroupPolicy\Machine\Citrix\GroupPolicy\*.*"
)
IF EXIST "C:\Windows\System32\GroupPolicy\User\Citrix\GroupPolicy" (
 DEL /S /Q "C:\Windows\System32\GroupPolicy\User\Citrix\GroupPolicy\*.*"
)
 
 
:: ================================================================================
:: Shutdown Build Server to put vDisk in Standard Mode
:: ================================================================================
ECHO %ShutdownMode%
IF %ShutdownMode%==REBOOTME (
 ECHO Build Server will be rebooted
 SHUTDOWN /R /F /T 20 /C "Build Server will be rebooted to apply all the changes & updates"
)
 
IF %ShutdownMode%==SHUTDOWNME (
 ECHO Shutdown Build Server to put vDisk in Standard Mode
 SHUTDOWN /S /F /T 20 /C "vDisk is ready to put in Standard mode"
)
 
 
IF %ShutdownMode%==ONLINE (
 ECHO Script will exit to allow vDisk Auto-Update to continue.
        ECHO Auto-Update requires a clean script exit to continue.
)
 
:: ===========================================================================================================
:: Disable Logons - This resolves users being able to login before the server is ready
:: ===========================================================================================================
SCHTASKS /RUN /TN Disable_Logons
 
:: ================================================================================
:: Disconnect Mapped Drives
:: ================================================================================
ECHO Disconnect Mapped Drives
NET USE * /D /Y
PING LOCALHOST -n 5 >NUL</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->