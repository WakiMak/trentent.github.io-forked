---
id: 571
title: PVS Target Device Update Script - Supplemental File, 1_pre-get_partition_and_netinfo.cmd / 2_post-remove_nonpresent_hardware_v33.cmd
date: 2015-03-20T15:12:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/pvs-target-device-update-script-supplemental-file-1_pre-get_partition_and_netinfo-cmd-2_post-remove_nonpresent_hardware_v33-cmd/
permalink: /2015/03/20/pvs-target-device-update-script-supplemental-file-1_pre-get_partition_and_netinfo-cmd-2_post-remove_nonpresent_hardware_v33-cmd/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/pvs-target-device-update-script_20.html
blogger_internal:
  - /feeds/7977773/posts/default/7188205959891715197
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
These are two scripts we use to remove nonpresent hardware from device manager.  These devices are generated when we update the Target Device Hardware and VMWare Tools and sit unused so are removed.  If there is ever modifications to the vDisk that generate additional hardware this script ensures the vDisk is as clean as possible. You'll need devcon.exe as well.

1\_pre-get\_partition\_and\_netinfo.cmd

```shell
@echo off
CLS
set _base=%~dp0
set _scriptname=%~n0@echo off
set adminfold=%systemdrive%\admin\conversion
set partinfo=%adminfold%\%computername%_partinfo.log
set netinfo=%adminfold%\%computername%_netinfo.log
if not exist "%adminfold%" (
    @echo Creating admin folder "%adminfold%"
    md "%adminfold%"
    ) ELSE (
    @echo "Admin folder [%adminfold%] already exists"
    )
@echo list volume | diskpart > "%partinfo%"
netsh interface ip dump > "%netinfo%"
ipconfig /all > "%netinfo%
```


2\_post-remove\_nonpresent\_hardware\_v33.cmd

```shell
@echo off
CLS
set _ver=Version 3.3 (April 2011)
REM Remove non present hardware in machines
REM Called by : Runs Independantly
REM Calls     : devcon.exe (32 bit or 64 bit)
REM by Simon Price
REM August, 2007
REM email : abitpricey@gmail.com | admin@vmuse.info
REM     Check for updates here: http://www.vmuse.info
REM
REM     Updated May 2008 - removed need for lmod - should work on x64 Windows
REM     Updated Mar 2010 - Added dated logfiles and volume shadow copy removal
REM     Update July 2010 - Added 'intelligence' to determine 64 or 32 bit Windows version
REM     Update April 2011 - Used different method for detemrination of x64 systems. Apparently not working on some systems.
 
 
SETLOCAL ENABLEDELAYEDEXPANSION
 
set _base=%~dp0
set _scriptname=%~n0
set _bin=%_base%_bin
path=%_base%_bin;%path%
 
 
set title=%_scriptname%
TITLE %title% %_ver%
 
 
echo ====================================================
echo =====Removing Non-Present hardware from Machine=====
echo ====================================================
echo.
echo Version %_ver%
echo Written by Simon Price
echo Questions / Problems / Suggestions - abitpricey@gmail.com 
echo or http://www.vmuse.info
echo Please Wait
 
set _outfoldroot=%systemdrive%\admin\%_scriptname%
if not exist "%_outfoldroot%" md "%_outfoldroot%"
 
 
if exist "%systemdrive%\*x86*" (
    set _bitver=x64
    ) else (
 
    set _bitver=x32
    )
 
@echo This OS is %_bitver%
 
path=%_bin%\%_bitver%;%path%
 
 
set _classsearch=VolumeSnapshot modem battery net CDROM DiskDrive display fdc hdc keyboard media monitor scsiadapter Printer scsi unknown floppydisk processor volume MultiFunction mouse battery hidclass system usb wceusbs SmartCardReader SmartCard Biometric SBP2 MultiPortSerial DellSerialDevice BiometricDevice TapeDrive usbstor 61883
 
for /f "tokens=*" %%a in ('ndate +%%y%%m%%d_%%H%%M%%S') do set _dttm=%%a
 
 
set _find=%_outfoldroot%\%computername%_%_dttm%_find.txt
set _removal=%_outfoldroot%\%computername%_%_dttm%_removal.txt
set _findall=%_outfoldroot%\%computername%_%_dttm%_findall.txt
set _classes=%_outfoldroot%\%computername%_%_dttm%_classes.txt
 
rem remove existing files
 
if exist "%_find%" del /q "%_find%"
if exist "%_findall%" del /q "%_findall%"
 
 
for %%a in (%_classsearch%) do (
rem devcon findall =%%a | sort | lmod /l* /b: [$1] | findstr /iv /c:"matching device" >> "%_findall%"
    for /f "tokens=1 delims=: " %%b in ('devcon findall ^=%%a ^| findstr /iv /c:"matching device"') do @echo %%b>> "%_findall%"
        )
    )
 
for %%a in (%_classsearch%) do (
 
rem devcon find =%%a | sort | lmod /l* /b: [$1] | findstr /iv /c:"matching device" >> "%_find%"
    for /f "tokens=1 delims=: " %%b in ('devcon find ^=%%a ^| findstr /iv /c:"matching device"') do @echo %%b>> "%_find%"
    )
 
 
REM List out the actual device classes
devcon classes > "%_classes%"
 
@echo. > "%_removal%"
 
set _found=0
 
for /f "tokens=* delims=" %%a in (%_findall%) do (
set fixEscapeChar=%%a
set fixEscapeChar=!fixEscapeChar:\_=\\_!
set fixEscapeChar=!fixEscapeChar:\{=\\{!
 
 
set _found=0
 
for /f "tokens=* delims=:" %%b in ('findstr /L /X /C:"!fixEscapeChar!" "%_find%"') do ( 
        set _found=1
        )
 
 
        if /i .!_found!.==.0. (
 
            for /f "tokens=1,2* delims=:" %%c in ('devcon find "@%%a" ^| findstr /iv /c:"matching device"') do (
            set _descr=%%d
 
            for /f "tokens=1 delims=\" %%A in ("%%c") do set _descr1=%%A
     
            )
             
             
 
            @echo removing device :  [!_descr1!]!_descr!
 
            set _removecmd=devcon remove "@%%a"
 
            for /f "tokens=* delims=" %%x in ('!_removecmd!') do set msg=%%x
            @echo -- "!msg!"
 
            @echo [!_descr1!],!_descr!,"%%a",!msg! >> "%_removal%"          
 
            )
 
 
    )
 
echo Complete. Output files are located here:
@echo [%_outfoldroot%]
echo.
 
 
echo.
 
goto :END
 
:SYNTAX
@echo.
@echo %message%
@echo SYNTAX : %_scriptname% ^<version info^> ^<logfile path^>
sleep 5
 
goto :END
 
 
:EN
```


&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->