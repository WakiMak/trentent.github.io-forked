---
id: 1607
title: Microsoft Patch (KB3170455) breaks PrintBRM importing printers with drivers
date: 2016-07-29T22:38:02-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1607
permalink: /2016/07/29/new-microsoft-patch-kb3170455-breaks-printbrm-importing-printers-with-drivers/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2016/07/BadPrintBRM_master.mp4
    192
    video/mp4
    
categories:
  - Blog
tags:
  - 2008R2
  - printing
  - Windows Update
---
We have an application (ARIA MO) that has some special requirements. Â The application requires all printers used by it in the different locations to be manually loaded on the Citrix server with all drivers. Â This totals around 220 or some odd printers installed with around 15 different drivers loaded. Â The printers must have a local port and cannot be mapped via TCPIP or network mapping.

Our design goals for our Citrix environment are to minimize the various PVS images we use so we use various 'layers' to allow a single master image to be able to host various unique and difficult configurations. Â For this application we use AppV as our layering technology to put the application on the server, but for the printers we use a script to loadÂ them onto the server. Â What we have is a print server that hosts all the printers needed by this application and we can export the printers into a file using Print Management. Â Then we save that file on a network share somewhere. Â When the Citrix server boots, I can take that file and manipulate its contents to change the queues to 'local' queues then import that modified file to the Citrix server. Â I configured the script to take two parameters, a print file name and a server name. Â I call the script with a command line like this:

<pre class="lang:batch decode:true">::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::  
::  Title: Install_Print_Queues.cmd
::  
::  By: Trentent Tye
::  
::  Date: 2014-09-05
::  
::  Description: This script will take an input (a print server) and enumerate all of its print queues and then
::  add them as global printers.  Administrator permissions are required for the user context this script is run
::  under as this script will also silently install print drivers.
::  
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Starting Adding Printers" >NUL
EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Starting importing printers from PrinterServer02" >NUL
powershell.exe -executionPolicy byPass -file "C:\PublishedApplications\AHS-PrintExportFile-Import.ps1" -serverName PrinterServer02 -printFile "\\fileserver\ctx_apps\ARIAMO\PrinterServer02.printerExport"
EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Completed importing printers from PrinterServer02" >NUL

EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Starting importing printers from printServer01" >NUL
powershell.exe -executionPolicy byPass -file "C:\PublishedApplications\AHS-PrintExportFile-Import.ps1" -serverName printServer01 -printFile "\\fileserver\ctx_apps\ARIAMO\PrintServer01.printerExport"
EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Completed importing printers from printServer01" >NUL
EVENTCREATE /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script - \"%~nx0"\" /D "Completed Adding Printers" >NUL
</pre>

The powershell command it calls is here:

<pre class="lang:ps decode:true">################################################################################################
#
#
# Created by:		Trentent Tye
#			        Intel Server Team
#			        IBM Canada Ltd.
#
# Creation Date:	Oct 24, 2014
# Modified Date:	
#
# File Name:		AHS-PrintExportFile-Import.ps1
#
# Description:		This script will take a parameter "-serverName" that is pointed at a
#                   print server and "-printFile" pointed at a '.printExport file.  What it will 
#                   do is create Local Ports from a .printerExport file that comes from the Print
#                   Server.  From this it will create the local ports with the proper UNC path 
#                   and port name.   
#
################################################################################################


Param(
  [string]$serverName,
  [string]$printFile
)

#check if eventlog exists, if not create it
$commandName = $MyInvocation.MyCommand
write-host $commandName


if ([System.Diagnostics.EventLog]::SourceExists("Local GP Startup Script - ""$commandName'""") -eq $False) {
    New-EventLog -LogName "Application" -Source ("Local GP Startup Script - ""$commandName'""")
   }

sleep 2

Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Starting PrinterExport-Import script")

sleep 2

Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Import-Module ServerManager")

ipmo ServerManager
add-WindowsFeature Print-Server

Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Getting NetView of {0}" -f $ServerName)
$netView = net view $serverName | findstr /i /C:"Print "
$netView = $netView -replace "\sPrint\s",","
$netView = $netView -replace "\s",""
$netView = $netView | ConvertFrom-CSV -Header "Printer"
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Number of printers detected: {0}" -f $netView.Printer.Count)

#Remove Printer Exports from server if they already exist
cd\
if (Test-Path -Path C:\printerExport) {
    Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Removing C:\printerExport")
    Remove-Item C:\printerExport -Force -Recurse
}

if (Test-Path -Path  C:\modified.printerExport) {
    Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Removing C:\modified.printerExport")
    Remove-Item C:\modified.printerExport
}

foreach ($printer in $netView) {
  $printer = $printer.printer
  reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Ports" /v \\$serverName\$printer /T REG_SZ /f
  reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Ports" /v \\$serverName\$printer /T REG_SZ /f
}

#need to restart the spooler to enable the ports
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Restarting print spooler services to enable ports")
net stop cpsvc /y
sleep 2
net stop spooler /y
sleep 2
net start spooler
sleep 2
net start cpsvc
sleep 2
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Completed print spooler services to enable ports")


#unpack printer properties file
mkdir C:\printerExport
C:\Windows\System32\spool\tools\PrintBRM.exe -R -D C:\printerExport -F $printFile
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Unpacked printerExport file")

#modify printer properties
echo `<PRINTERPORTS`>`</PRINTERPORTS`> > "C:\printerExport\BrmPorts.xml"

#configure print queues
cd C:\printerExport\Printers

Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Configuring Print Queues")
$files = ls
foreach ($file in $files) {
    $xml = [xml](Get-Content $file)
    #blank sharename
    $xml.PRINTQUEUE.ShareName = ""
    #set attributes to 2624 which disables sharing
    $xml.PRINTQUEUE.Attributes = "2624"
    #configure portName to include \\server\printer
    $portName ="\\$serverName\"
    $portName += $xml.PRINTQUEUE.PortName | out-String
    $portName = $portName -replace "`n|`r"
    $xml.PRINTQUEUE.PortName = $portName
    $xml.PRINTQUEUE
    $path = "C:\printerExport\Printers\$file"
    $xml.Save($path)
}

# repack printerExport
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Repacking printerExport")
C:\Windows\System32\spool\tools\PrintBRM.exe -B -D C:\printerExport -F C:\modified.printerExport

# windows firewall needs to be enabled to import
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Starting Windows Firewall to import printers")

sc.exe config MpsSvc start= auto
sleep 2
net start MpsSvc
sleep 2

# import changed file
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Importing print queues")
C:\Windows\System32\spool\tools\PrintBRM.exe -R -F C:\modified.printerExport
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Completed importing print queues")

sleep 2

Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Stopping and disabling Windows Firewall")
net stop MpsSvc /y
sc.exe config MpsSvc start= disabled

#need to restart the spooler to alphabeticalize the printer list
sleep 2
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Restarting print services to alphabeticalize printer list")
net stop cpsvc /y
sleep 2
net stop spooler /y
sleep 2
net start spooler
sleep 2
net start cpsvc
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Finished restarting print services to alphabeticalize printer list")

sleep 2
Write-EventLog -LogName APPLICATION -Source "Local GP Startup Script - ""$commandName'""" -EntryType Information -EventID 1 -Message ("Completed PrinterExport-Import script")</pre>

So, what does this have to do withÂ KB3170455? Â Well, since installing KB3170455 it prevents importing printer files with print drivers embedded. Â This is what it looks like with KB3170455 installed:

<img class="aligncenter wp-image-1608 size-large" src="http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.04.48-AM-1024x167.png" alt="Screen Shot 2016-07-29 at 9.04.48 AM" width="1024" height="167" srcset="http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.04.48-AM-1024x167.png 1024w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.04.48-AM-300x49.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.04.48-AM-768x125.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.04.48-AM.png 1164w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

Without:  
<img class="aligncenter size-full wp-image-1610" src="http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.07.35-AM.png" alt="Screen Shot 2016-07-29 at 9.07.35 AM" width="1166" height="186" srcset="http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.07.35-AM.png 1166w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.07.35-AM-300x48.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.07.35-AM-768x123.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/Screen-Shot-2016-07-29-at-9.07.35-AM-1024x163.png 1024w" sizes="(max-width: 1166px) 100vw, 1166px" /> 

And the failure import with 3170455 installed:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1607-9" width="1140" height="705" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/07/BadPrintBRM_master.mp4?_=9" /><a href="http://theorypc.ca/wp-content/uploads/2016/07/BadPrintBRM_master.mp4">http://theorypc.ca/wp-content/uploads/2016/07/BadPrintBRM_master.mp4</a></video>
</div>

Remove KB3170455 and the import works without issue.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->