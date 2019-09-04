---
id: 545
title: Symantec Endpoint Protection (SEP) 12 virus definition download and installation script
date: 2015-09-23T11:40:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/23/symantec-endpoint-protection-sep-12-virus-definition-download-and-installation-script/
permalink: /2015/09/23/symantec-endpoint-protection-sep-12-virus-definition-download-and-installation-script/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/symantec-endpoint-protection-sep-12.html
blogger_internal:
  - /feeds/7977773/posts/default/5695482746527700403
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
---
A quick and dirty script to download and install the latest SEP12 virus definitions from Symantec's FTP site. Â We use this script to force the latest updates when we update our Citrix PVS vDisk.

This script output looks like so:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-BQGESWMAzNs/VgLi-7bYSMI/AAAAAAAABJI/rWqC1GBGGD8/s1600/Screen%2BShot%2B2015-09-23%2Bat%2B11.35.07%2BAM.png"><img src="http://4.bp.blogspot.com/-BQGESWMAzNs/VgLi-7bYSMI/AAAAAAAABJI/rWqC1GBGGD8/s320/Screen%2BShot%2B2015-09-23%2Bat%2B11.35.07%2BAM.png" width="320" height="119" border="0" /></a>
</div>

This script also requires the [FTP for powershell module](https://gallery.technet.microsoft.com/scriptcenter/PowerShell-FTP-Client-db6fe0cb). Â Download the [entire script and dependency here](https://theorypc-my.sharepoint.com/personal/trententtye_theorypc_onmicrosoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=qmvKsQL%2BNHR%2FL%2Fq7JQJfEVm9q0nq7YvIpodh1d07Wjg%3D&docid=088d55459a844472dbcc1249ecee2837c).

<pre class="lang:ps decode:true ">#######################################################################################################
#
#
#  Created by Trentent Tye
#             IBM Intel Server Team
#             September 22, 2015
#
# Description: This script will download the latest Symantec antivirus files from Symantec's FTP site
# and execute the install to update them.  This script borrows elements from Zebbelin's Symantec script
# for the GUI, etc.
#
# Virus definitions URL is located here: 
#      http://www.symantec.com/security_response/definitions/download/detail.jsp?gid=sep
#
# FTP site with the definitions are located here:
#      ftp://ftp.symantec.com/public/english_us_canada/antivirus_definitions/norton_antivirus/
#
# Only tested on 64bit 2008R2 as of Sept 22 2015 but I tried to write it for 32bit arch as well
#
#######################################################################################################
 
# Requires PowerShell FTP module: https://gallery.technet.microsoft.com/scriptcenter/PowerShell-FTP-Client-db6fe0cb
 
$scriptDirectory = Split-Path -Path $MyInvocation.MyCommand.Definition -Parent
cd $scriptDirectory
Import-Module ".\PSFTP.psm1"
 
#If 2003 or 2008 set path Symantec path accordingly:
$OSVersion = (Get-WmiObject Win32_OperatingSystem).Version
 
if ($OSVersion -eq "5.2.3790") {
    $rootPath = "C:\Documents and Settings\All Users\Application Data"
}
 
if ($OSVersion -eq "6.1.7601") {
    $rootPath = "C:\ProgramData"
}
 
 
# create anonymous credentials
$cred = new-object System.Net.NetworkCredential('anonymous','','')
 
# Set-FTPConnection returns a credential error...  ignore it.  It actually works.
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Connecting to Symantec FTP site"
Set-FTPConnection -Server ftp://ftp.symantec.com/public/english_us_canada/antivirus_definitions/norton_antivirus/ -Credentials $cred -Session VirusDefDownload -UsePassive -ErrorAction silentlycontinue | out-null
 
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Getting list of items on the FTP site"
$Session = Get-FTPConnection -Session VirusDefDownload 
$listOfFTPItems = Get-FTPChildItem -Session $Session
 
# Symantec Endpoint Protection Client Installations on Windows Platforms (64-bit)
# Use the v5i64 executable file for 64-bit client installations and v5i32 for 32-bit installations.
 
If ($env:PROCESSOR_ARCHITECTURE -eq "AMD64") {
  $listOfFTPItems = $listOfFTPItems | Where-Object {$_.name -like "*-v5i64*"}
} else {
  $listOfFTPItems = $listOfFTPItems | Where-Object {$_.name -like "*-v5i32*"}
}
 
#we are relying on Symantec keeping the name arranged by properdate on the file.  If they change their name scheme
# YYYYMMDD-### then this sort method may not work.
$virusDefs = $listOfFTPItems | sort-object Name | select -Last 1
 
#compare current virusDefs to the definitions actually installed.  Take the FTP name format, remove -v5i##.exe, swap
#hypens "-" for "." then do a compare against the virusDefs in this folder:
#C:\ProgramData\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs
If ($env:PROCESSOR_ARCHITECTURE -eq "AMD64") {
  $virusDefsFTPName = $virusDefs.Name.Replace('-v5i64.exe','')
} else {
  $virusDefsFTPName = $virusDefs.Name.Replace('-v5i32.exe','')
}
 
$virusDefsFTPName = $virusDefsFTPName.Replace('-','.')
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": FTP Virus Definition Name:" $virusDefs.FullName
 
 
#remove old virus definitions
Function RemoveOldDefs {
    $allDefs = (dir $rootPath"\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs" | where-object {$_.name -like "*20*"}).Name
 
    #if there is only one set of virus definitions folder we are done (you can't remove it) -- measure.count enables count on 2003 with PowerShell 2
    if (($allDefs | measure).Count -eq 1) { 
        break 
    } else {
        [System.Collections.ArrayList]$ArrayList = $allDefs
        $ArrayList.Remove("$virusDefsFTPName")
        write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
        write-host ": Removing old definitions:"
        write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
        write-host ": $ArrayList"
        foreach ($def in $ArrayList) {
          rmdir $rootPath"\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs\$def" -Recurse
        }
    }
}
 
 
#compare folder name with virusDefs on FTP site.  If true this will exit the script.
If (test-path $rootPath"\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs\$virusDefsFTPName") {
    write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
    write-host ": Local Virus definitions match latest FTP definitions"
    write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
    write-host ": $virusDefsFTPName vs "(dir $rootPath"\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs" | where-object {$_.name -like "*20*"}).Name
    RemoveOldDefs
  Exit
  }
 
#Remove any previous download if one exists
if (test-path ($env:TEMP + "\" + $virusDefs.Name)) {
    remove-item ($env:TEMP + "\" + $virusDefs.Name) -Force
}
 
 
 
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Virus definitions do not match"
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": $virusDefsFTPName vs "(dir $rootPath"\Symantec\Symantec Endpoint Protection\CurrentVersion\Data\Definitions\VirusDefs" | where-object {$_.name -like "*20*"}).Name
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Downloading and installing newest definitions:" $virusDefs.Name 
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Starting Download"
 
#Environment for File Download
[void][reflection.assembly]::LoadWithPartialName("Microsoft.VisualBasic")
$object = New-Object Microsoft.VisualBasic.Devices.Network
 
$Script:DownloadURL  =  $virusDefs.FullName
$SCRIPT:DownloadPATH = $env:TEMP + "\" + $virusDefs.Name
$SCRIPT:object.DownloadFile($DownloadURL, $DownloadPATH, "", "", $true, 10000, $true, "DoNothing")
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Finished Download"
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Starting Installation"
 
#install Defs
$downloadedDefs = $env:TEMP + "\" + $virusDefs.Name
Start-Process $downloadedDefs -ArgumentList /q -Wait
 
write-host -NoNewLine (get-date -Format HH:mm:ss) -ForegroundColor "Green"
write-host ": Installation Completed"
sleep 2
 
#remove old definitions
RemoveOldDefs
 
#Remove download if one exists
if (test-path ($env:TEMP + "\" + $virusDefs.Name)) {
    remove-item ($env:TEMP + "\" + $virusDefs.Name) -Force
}</pre>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->