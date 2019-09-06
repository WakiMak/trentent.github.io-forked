---
id: 519
title: Automatically Defrag your Citrix PVS vDisks
date: 2016-04-19T15:17:00-06:00
author: trententtye
layout: post
permalink: /2016/04/19/automatically-defrag-your-citrix-pvs-vdisks/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/04/automatically-defrag-your-citrix-pvs.html
blogger_internal:
  - /feeds/7977773/posts/default/7048726776245551791
categories:
  - Blog
tags:
  - Citrix
  - PowerShell
  - Provisioning Services
  - PVS
---
Here is a script to automate 'offline defragging' your PVS vDisks. Â [Why Defrag? Â It's explained nicely here](https://www.citrix.com/blogs/2015/01/19/size-matters-pvs-ram-cache-overflow-sizing/?_ga=1.24764090.1091830672.1452712354).

> <span style="font-family: Arial, Helvetica, sans-serif;"><b>Effects of Fragmentation on Write Cache Size</b><br /> The culprit above in excessive write cache size is fragmentation. This is caused because PVS redirection is block based and not file based. The redirection to the write cache is based on the logical block level structure of the vDisk. To illustrate this, consider the scenario below.</span>

And the script:

<pre class="lang:ps decode:true">############################################################################################
#
# Title:       Citrix PVS vDisk Defrag
#
# Name:        Trentent Tye
#
# Date:        2016-04-19
#
# Description: This script was designed to automate defragging vDisks.  The variables underneath
#              are hardcoded (Bad Trentent!) but they should become parameterized to make this
#              dynamic.  On the To Do.  This was just a quick and dirty script to get this
#              done...
#    
#    #Prerequistes
#    Citrix PVS Console (7.7 used while developing this script)
#    Appropriate permissions to login to  PVS
#    Martin Zugec's MCLI to PowerShell https://www.citrix.com/blogs/2012/07/23/provisioning-services-and-powershell-easier-method/
#      ^^ fixed up: http://trentent.blogspot.ca/2016/04/convertfrom-mcli-for-pvs-76-posh-module.html
#    
#    
############################################################################################
 
 
###########################################VARIABLES##########################################
#PVS Server
$PVSServer = "PVS04"
#PVS Port number
$PVSPort = "54321"
$TargetvDiskName = "&65Tn05"
$PVSStore = "&"
$PVSSite = "BDC"
 
#Domain used
$PVScred = Get-Credential -Message "Enter your credentials for Citrix PVS (DOMAIN\Username):"
 
 
###########################################VARIABLES##########################################
 
 
#Add PowerShell MCLI for PVS
#$installutil = $env:systemroot + '\Microsoft.NET\Framework64\v4.0.30319\installutil.exe'
#&$installutil "C:\Program Files\Citrix\Provisioning Services Console\McliPSSnapIn.dll"
add-PSSnapIn mclipssnapin
 
#add ConvertFrom-MCLI
$ScriptDir = Split-Path $script:MyInvocation.MyCommand.Path
. (Join-Path -Path $ScriptDir -ChildPath 'PVS-ConvertToPosh.ps1')
 
 
####SETUP CONNECTIONS####
#Setup PVS server connection, MCLI-Run appears to only  plain text for the username and password...  We will convert the secure string password 
#to plain text then blank it, and convert the username credential to a string to prevent an object being passed.
Write-Host "Connecting to PVS..." -ForegroundColor Yellow
$PVSUser = $PVScred.UserName.ToString()
$PVSPassPlainText = [Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($PVSCred.Password))
try {
    MCLI-Run setupconnection -p server=$PVSServer,port=$PVSPort,user=$PVSUser,password=$PVSPassPlainText -ErrorAction Stop
} catch {
    Write-Host "Connection to PVS Failed" -ForegroundColor Yellow
    Write-Host "Perhaps your credentials are incorrect?" -ForegroundColor Yellow
    write-host "$_"
    pause
}
$PVSPassPlainText = ""
 
 
#First check, is the target vDisk set to prod?
Write-Host "Checking if the target vDisk is set to Prod..." -ForegroundColor Yellow
$diskver = mcli-get diskversion -p diskLocatorName=$TargetvDiskName,siteName=$PVSSite,storeName=$PVSStore
 
#access reports back the following values:
#0 (Production), 1 (Maintenance), 2 (MaintenanceHighestVersion), 3 (Override), 4 (Merge), 5(MergeMaintenance), 6 (MergeTest), and 7 (Test) Min=0, Max=7, Default=0
$diskobj = $diskver | convertfrom-MCLI
foreach ($diskprop in $diskobj) {
  if ($diskprop.access -ne "0") {
    write-host "vDisks need to be in a good production state. A maintenance or test vDisk version was detected.`nPlease remove or promote this version before continuing."
    write-host "Disk detected in maintenance mode: " $diskprop.diskFileName
    pause
    #exit
    }
}
 
#all vDisks in chains are in production state. Creating new version...
Write-Host "Creating Maintenance version..." -ForegroundColor Yellow
$error.Clear()
$createMaintenance = mcli-runwithreturn createmaintenanceversion -p diskLocatorName=$TargetvDiskName,siteName=$PVSSite,storeName=$PVSStore
 
#check to see if creation of the Maintenance version was successful
if ($error.Count -gt 0) {
    write-host "vDisk creation failed."
    write-host $createMaintenance
    pause
}
 
#set the description of the vDisk to identify who has run it:
$diskver = mcli-get diskversion -p diskLocatorName=$TargetvDiskName,siteName=$PVSSite,storeName=$PVSStore
$maintenanceVersion = ($diskver | convertfrom-MCLI).version[-1]
Write-Host "Setting Maintenance version description..." -ForegroundColor Yellow
Mcli-Set DiskVersion -p diskLocatorName=$TargetvDiskName,siteName=$PVSSite,storeName=$PVSStore,version=$maintenanceVersion -r description="$env:username defragged the vDisk"
 
#get the vDisk filename from the return value:
$vDiskFileName = $createMaintenance.split("= ")[-1]
#get the serverStore path for the file:
$storePath = mcli-get serverstore -p servername=$PVSServer,storename=$PVSStore | ConvertFrom-MCLI
#mount the new version of the vDisk on this server
Write-Host "Mounting vDisk..." -ForegroundColor Yellow
$pathTovDiskVersion = $storePath.path + "\" + $vDiskFileName
. "$env:ProgramFiles\Citrix\Provisioning Services\CVhdMount.exe" -p 1 $pathTovDiskVersion
#get the driveLetter of the attached vDisk:
sleep 5
$vDiskDriveLetter = (Mcli-RunWithReturn MappedDriveLetter).split("= ")[-1] + ":"
if ($vDiskDriveLetter -eq ":") {
    write-host $vDiskDriveLetter
    write-host (Mcli-RunWithReturn MappedDriveLetter)
    write-host "CVHDMOUNT failed"
    pause
    }
 
#https://blogs.technet.microsoft.com/askperf/2008/03/14/disk-fragmentation-and-system-performance/
#defrag switches:
#-c: Defragments all volumes on the system.  You can use this switch without needing to specify a drive letter or mount point
#-a: Perform an analysis of the selected drive and provides a summary output (shown below):
#-r: Performs a partial defragmentation by consolidating only file fragments that are less than 64MB in size.  This is the default setting
#-w: Performs a full defragmentation by consolidating all file fragments regardless of size
#-f: Force defragmentation of the volume even if the amount of free space is lower than normally required.  When running this, be aware that it can result in slow system performance while the defragmentation is occurring
#-v: Displays verbose reports.  When used in combination with the -a switch, only the analysis report is displayed.  When used alone, both the analysis and defragmentation reports are shown.
#-i: Runs the defragmentation in the background and only if the system is idle
#-b: Optimizes boot files and applications, but leaves the rest of the drive untouched.  Only works on boot drive
defrag $vDiskDriveLetter -w -f -v
 
. "$env:ProgramFiles\Citrix\Provisioning Services\CVhdMount.exe" -u 0
 
#promote version to production
Write-Host "Promoting vDisk to production..."  -ForegroundColor Yellow
mcli-run PromoteDiskVersion -p diskLocatorName=$TargetvDiskName,siteName=$PVSSite,storeName=$PVSStore</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->