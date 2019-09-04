---
id: 1922
title: 'AppV5 &#8211; Automated Sequencer with Citrix PVS'
date: 2017-01-05T16:21:15-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1922
permalink: /?p=1922
categories:
  - Blog
tags:
  - AppV
  - Citrix
  - PowerShell
  - Provisioning Services
  - PVS
  - scripting
---
<p style="text-align: left;">
  This blog article¬ will detail the procedures required to setup an AppV5 automated sequencer on a Citrix Provisioned system.¬ The reason for using Citrix PVS is we do not have to worry about hyper-visors, snapshots, expired machine account passwords and for consistency.¬ PVS systems simply need a reboot to revert back to their original state and can be deployed en-masse to scale up and down as needed.¬ If you‚Äôre not running Citrix PVS in your environment it‚Äôs ~$150CAD for a Citrix XenDesktop Enterprise per User license to acquire a license for the software.
</p>

<h1 style="text-align: left;">
  1¬ ¬ ¬ ¬ ¬ VM Setup &#8211; Overview
</h1>

<table class=" alignleft">
  <tr>
    <td style="text-align: left;" width="26">
      1.
    </td>
    
    <td style="text-align: left;" width="598">
      The AppV5 automated sequencer has been designed to have a file share configured with appropriate permissions so that users can remotely drop files onto it and have it kick off sequencing.¬ It does this by using a scheduled task that monitors the share for specific files and starts sequencing when those files are present.¬ If you specify the package to be saved to a share, the task must be configured with a service account that has appropriate permissions to that share.
    </td>
  </tr>
  
  <tr>
    <td style="text-align: left;" width="26">
      2
    </td>
    
    <td width="598">
      <p style="text-align: left;">
        Workflow:
      </p>
      
      <p style="text-align: left;">
        <img class="aligncenter size-full wp-image-1923" src="http://theorypc.ca/wp-content/uploads/2017/01/Auto_Sequencing_Workflow.png" alt="" width="489" height="433" srcset="http://theorypc.ca/wp-content/uploads/2017/01/Auto_Sequencing_Workflow.png 489w, http://theorypc.ca/wp-content/uploads/2017/01/Auto_Sequencing_Workflow-300x266.png 300w" sizes="(max-width: 489px) 100vw, 489px" />
      </p>
      
      <p style="text-align: left;">
        1)¬ ¬ ¬ ¬ ¬ Files with silent installer and config.xml put onto the file share on AppV Sequencer.
      </p>
      
      <p style="text-align: left;">
        2)¬ ¬ ¬ ¬ ¬ Schedule Task ingests XML file
      </p>
      
      <p style="text-align: left;">
        3)¬ ¬ ¬ ¬ ¬ Sequencing starts
      </p>
      
      <p style="text-align: left;">
        4)¬ ¬ ¬ ¬ ¬ Package is made
      </p>
      
      <p style="text-align: left;">
        5)¬ ¬ ¬ ¬ ¬ Package is saved to desired location
      </p>
      
      <p style="text-align: left;">
        </td> </tr> </tbody> </table> 
        
        <h1 style="text-align: left;">
          2¬ ¬ ¬ ¬ ¬ Setup the Sequencer
        </h1>
        
        <h2 style="text-align: left;">
          2.1¬ ¬ ¬ ¬ ¬ File Share
        </h2>
        
        <table class=" alignleft" width="638">
          <tr>
            <td width="36">
            </td>
            
            <td width="603">
              The following pre-requisites are required for this to work:</p> 
              
              <p>
                &#8211;¬ ¬ ¬ ¬ ¬ ¬ Sequencer must be on a PVS Target Device
              </p>
              
              <p>
                &#8211;¬ ¬ ¬ ¬ ¬ ¬ AppVAutoSequencer.ps1 script present on system
              </p>
              
              <p>
                &#8211;¬ ¬ ¬ ¬ ¬ ¬ Shared folder for application install files
              </p>
              
              <p>
                &#8211;¬ ¬ ¬ ¬ ¬ ¬ (optional) Service Account for running scheduled task and permissions on destination share</td> </tr> 
                
                <tr>
                  <td width="36">
                    1
                  </td>
                  
                  <td width="603">
                    <strong><u>While in Private or Maintence mode on the AppV Sequencer vDisk</u>. </strong>Open Share and Storage Management</p> 
                    
                    <p>
                      <img class="aligncenter size-full wp-image-1924" src="http://theorypc.ca/wp-content/uploads/2017/01/image002.png" alt="" width="407" height="508" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image002.png 407w, http://theorypc.ca/wp-content/uploads/2017/01/image002-240x300.png 240w" sizes="(max-width: 407px) 100vw, 407px" /></td> </tr> 
                      
                      <tr>
                        <td width="36">
                          2
                        </td>
                        
                        <td width="603">
                          Select ‚ÄòProvision Share‚Äô</p> 
                          
                          <p>
                            <img class="aligncenter size-full wp-image-1925" src="http://theorypc.ca/wp-content/uploads/2017/01/image003.png" alt="" width="260" height="292" /></td> </tr> 
                            
                            <tr>
                              <td width="36">
                                3
                              </td>
                              
                              <td width="603">
                                Select the share location</p> 
                                
                                <p>
                                  <img class="aligncenter size-full wp-image-1926" src="http://theorypc.ca/wp-content/uploads/2017/01/image004.png" alt="" width="723" height="570" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image004.png 723w, http://theorypc.ca/wp-content/uploads/2017/01/image004-300x237.png 300w" sizes="(max-width: 723px) 100vw, 723px" /></td> </tr> 
                                  
                                  <tr>
                                    <td width="36">
                                      4
                                    </td>
                                    
                                    <td width="603">
                                      Choose ‚ÄòYes, change NTFS permissions‚Äô</p> 
                                      
                                      <p>
                                        <img class="aligncenter size-full wp-image-1927" src="http://theorypc.ca/wp-content/uploads/2017/01/image005.png" alt="" width="721" height="572" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image005.png 721w, http://theorypc.ca/wp-content/uploads/2017/01/image005-300x238.png 300w" sizes="(max-width: 721px) 100vw, 721px" /></td> </tr> 
                                        
                                        <tr>
                                          <td width="36">
                                            5
                                          </td>
                                          
                                          <td width="603">
                                            If this is a part of the domain add whatever permissions are necessary to allow users to save files to this location and click ‚ÄúOK‚Äù then ‚ÄúNext‚Äù:</p> 
                                            
                                            <p>
                                              <img class="aligncenter size-full wp-image-1928" src="http://theorypc.ca/wp-content/uploads/2017/01/image006.png" alt="" width="366" height="440" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image006.png 366w, http://theorypc.ca/wp-content/uploads/2017/01/image006-250x300.png 250w" sizes="(max-width: 366px) 100vw, 366px" /></td> </tr> 
                                              
                                              <tr>
                                                <td width="36">
                                                  6
                                                </td>
                                                
                                                <td width="603">
                                                  Specify the SMB share name and click ‚ÄòNext‚Äô</p> 
                                                  
                                                  <p>
                                                    <img class="aligncenter size-full wp-image-1929" src="http://theorypc.ca/wp-content/uploads/2017/01/image007.png" alt="" width="718" height="573" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image007.png 718w, http://theorypc.ca/wp-content/uploads/2017/01/image007-300x239.png 300w" sizes="(max-width: 718px) 100vw, 718px" /></td> </tr> 
                                                    
                                                    <tr>
                                                      <td width="36">
                                                        7
                                                      </td>
                                                      
                                                      <td width="603">
                                                        Click ‚ÄòNext‚Äô</p> 
                                                        
                                                        <p>
                                                          <img class="aligncenter size-full wp-image-1930" src="http://theorypc.ca/wp-content/uploads/2017/01/image008.png" alt="" width="720" height="572" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image008.png 720w, http://theorypc.ca/wp-content/uploads/2017/01/image008-300x238.png 300w" sizes="(max-width: 720px) 100vw, 720px" /></td> </tr> 
                                                          
                                                          <tr>
                                                            <td width="36">
                                                              8
                                                            </td>
                                                            
                                                            <td width="603">
                                                              Select ‚ÄòUsers and groups have custom permissions‚Äô and click ‚ÄòPermissions‚Äô.¬ Set ‚ÄòEveryone‚Äô to ‚ÄòFull Control‚Äô and click ‚ÄòOK‚Äô and ‚ÄòNext‚Äô</p> 
                                                              
                                                              <p>
                                                                <img class="aligncenter size-full wp-image-1931" src="http://theorypc.ca/wp-content/uploads/2017/01/image009.png" alt="" width="1020" height="571" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image009.png 1020w, http://theorypc.ca/wp-content/uploads/2017/01/image009-300x168.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/image009-768x430.png 768w" sizes="(max-width: 1020px) 100vw, 1020px" /></td> </tr> 
                                                                
                                                                <tr>
                                                                  <td width="36">
                                                                    9
                                                                  </td>
                                                                  
                                                                  <td width="603">
                                                                    Click ‚ÄòNext‚Äô</p> 
                                                                    
                                                                    <p>
                                                                      <img class="aligncenter size-full wp-image-1932" src="http://theorypc.ca/wp-content/uploads/2017/01/image010.png" alt="" width="717" height="573" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image010.png 717w, http://theorypc.ca/wp-content/uploads/2017/01/image010-300x240.png 300w" sizes="(max-width: 717px) 100vw, 717px" /></td> </tr> 
                                                                      
                                                                      <tr>
                                                                        <td width="36">
                                                                          10
                                                                        </td>
                                                                        
                                                                        <td width="603">
                                                                          Click ‚ÄòCreate‚Äô</p> 
                                                                          
                                                                          <p>
                                                                            <img class="aligncenter size-full wp-image-1933" src="http://theorypc.ca/wp-content/uploads/2017/01/image011.png" alt="" width="718" height="573" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image011.png 718w, http://theorypc.ca/wp-content/uploads/2017/01/image011-300x239.png 300w" sizes="(max-width: 718px) 100vw, 718px" /></td> </tr> 
                                                                            
                                                                            <tr>
                                                                              <td style="text-align: left;" width="36">
                                                                                11
                                                                              </td>
                                                                              
                                                                              <td width="603">
                                                                                <p style="text-align: left;">
                                                                                  Click Close
                                                                                </p>
                                                                                
                                                                                <p>
                                                                                  <img class="aligncenter size-full wp-image-1934" src="http://theorypc.ca/wp-content/uploads/2017/01/image012.png" alt="" width="717" height="571" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image012.png 717w, http://theorypc.ca/wp-content/uploads/2017/01/image012-300x239.png 300w" sizes="(max-width: 717px) 100vw, 717px" /></td> </tr> </tbody> </table> 
                                                                                  
                                                                                  <h2 style="text-align: left;">
                                                                                    2.2¬ ¬ ¬ ¬ ¬ Scheduled Task
                                                                                  </h2>
                                                                                  
                                                                                  <table class=" alignleft" style="height: 11671px;" width="800">
                                                                                    <tr>
                                                                                      <td width="36">
                                                                                      </td>
                                                                                      
                                                                                      <td width="603">
                                                                                        The following pre-requisites are required for this to work:</p> 
                                                                                        
                                                                                        <p>
                                                                                          &#8211;¬ ¬ ¬ ¬ ¬ ¬ Sequencer must be on a PVS Target Device
                                                                                        </p>
                                                                                        
                                                                                        <p>
                                                                                          &#8211;¬ ¬ ¬ ¬ ¬ ¬ AppVAutoSequencer.ps1 script present on system
                                                                                        </p>
                                                                                        
                                                                                        <p>
                                                                                          &#8211;¬ ¬ ¬ ¬ ¬ ¬ Shared folder for application install files
                                                                                        </p>
                                                                                        
                                                                                        <p>
                                                                                          &#8211;¬ ¬ ¬ ¬ ¬ ¬ (optional) Service Account for running scheduled task and permissions on destination share</td> </tr> 
                                                                                          
                                                                                          <tr>
                                                                                            <td width="36">
                                                                                              1
                                                                                            </td>
                                                                                            
                                                                                            <td width="603">
                                                                                              <strong><u>While in Private or Maintence mode on the AppV Sequencer vDisk</u>. </strong>Copy the ‚ÄòAppVAutoSequencer.ps1‚Äô script onto the target device
                                                                                            </td>
                                                                                          </tr>
                                                                                          
                                                                                          <tr>
                                                                                            <td width="36">
                                                                                              2
                                                                                            </td>
                                                                                            
                                                                                            <td width="603">
                                                                                              &nbsp;</p> 
                                                                                              
                                                                                              <pre class="width-set:true scroll:true lang:ps decode:true">&lt;# ------------------------------------------------------------------------------------------------
Purpose.: This script runs on a scheduled task to injest an XML file and produce an AppV package
Author..: Trentent Tye : trententtye@hotmail.com

Usage...: AppVAutoSequencer.ps1

Changes.: v1.0.2016.12.06: Trentent Tye: Initial release
		  
--------------------------------------------------------------------------------------------------- #&gt;
$VerbosePreference = "Continue"
Start-Transcript -Path "$env:TEMP\transcript.log"

#Variables
$localSharePath = "C:\swinst\AutoSequence" #path to the shared folder local on this machine
$templateFilePath = "D:\Template.appvt" #path the AppV template file
$overwriteExistingPackage = $true  #if this is true then any package with the same name in the "save" location will be deleted


function monitorFiles {

    if (!(test-path $localSharePath)) {
        write-verbose -message "Directory $localsharePath was not found."
        exit
    }
    #monitor for installation files to be present  -- https://blogs.technet.microsoft.com/heyscriptingguy/2012/07/17/use-powershell-to-monitor-for-the-creation-of-new-files/
    $queryPath = $localSharePath.Replace("\","\\\\")
    $query = @"
Select * from __InstanceCreationEvent within 10
where targetInstance isa 'Cim_DirectoryContainsFile'
and targetInstance.GroupComponent = 'Win32_Directory.Name="$queryPath"'
"@

    if ((Get-Eventsubscriber).SourceIdentifier -eq "MonitorFiles") { 
        #previous eventsubscriber found.  Cleaning up.
        Get-EventSubscriber | Unregister-Event
        Get-Event | Remove-Event
    }
    Register-WmiEvent -Query $query -SourceIdentifier ‚ÄúMonitorFiles‚Äù

    #wait for files to be detected
    write-verbose -message "Waiting for new files to appear in $localSharePath" 
    Wait-Event -SourceIdentifier "MonitorFiles" | Out-Null
    write-verbose -message "New files detected in $localSharePath at $((Wait-Event -SourceIdentifier `"MonitorFiles`").TimeGenerated)"

    #cleanup file wait event
    Get-EventSubscriber | Unregister-Event
    Get-Event | Remove-Event

    #wait for config.xml then ensure there have been no changes to the directory for 1 min
    write-verbose -message "Checking for config.xml"
    do {
        if (!(Test-Path "$localSharePath\config.xml")) {
            write-verbose -message "Waiting on config.xml..."
        }
        sleep 30
    }
    until (Test-Path "$localSharePath\config.xml")
    
    #check directory and see if there are any differences in a 60sec time span (hopefully allows long copies to finish)
    write-verbose -message "Checking for consistency in the share"
    do {
        if ($global:dirStart -ne $null) {
            if (diff $global:dirStart $global:dirNow -property FullName| Where-Object {$_.SideIndicator -eq "=&gt;" -or "&lt;="}) {
                write-verbose -message "Differences in the file system detected.  Still copying?"
            }
        }
        $global:dirStart = ls $localSharePath -Recurse
        sleep 60
        $global:dirNow = ls $localSharePath -Recurse
    }
    until (!(diff $global:dirStart $global:dirNow -property FullName| Where-Object {$_.SideIndicator -eq "=&gt;" -or "&lt;="}))
}

function readConfigXML {
    #config.xml should exist and all needed files should be present.  Injest XML
    [xml]$ConfigXML = get-content -path "$localSharePath\config.xml"

    $ConfigValues =@()

    #create variables
    $applicationName = $ConfigXML.settings.Application.ApplicationName
    $sendReportTo = $ConfigXML.settings.Application.SendReportTo
    $installScript = $ConfigXML.settings.Application.installScript
    $PackageName = $ConfigXML.settings.Application.PackageName
    $FullLoad = [bool]$ConfigXML.settings.Application.FullLoad
    $SavePackageTo = $ConfigXML.settings.Application.SavePackageTo
    $PVAD = $ConfigXML.settings.Application.PVAD

    if ($FullLoad -eq $true) {
    [string]$fullLoadCommand = "-FullLoad"
    } else {
    $fullLoadCommand = ""
    }

    if ($PVAD -ne "") {
    $PVADCommand = "-PrimaryVirtualApplicationDirectory $PVAD"
    }

    write-verbose -message "Application Name : $applicationName"
    write-verbose -message "Email report to  : $sendReportTo"
    write-verbose -message "Install Script   : $installScript"
    write-verbose -message "PackageName      : $PackageName"
    write-verbose -message "Full Load        : $fullLoadCommand"
    write-verbose -message "Save Package To  : $SavePackageTo"
    write-verbose -message "PVAD             : $PVADCommand"
    
    $configXMLdata = New-Object System.Object
    $configXMLdata | Add-Member -type NoteProperty -name applicationName -Value $applicationName
    $configXMLdata | Add-Member -type NoteProperty -name sendReportTo -Value $sendReportTo
    $configXMLdata | Add-Member -type NoteProperty -name installScript -Value $installScript
    $configXMLdata | Add-Member -type NoteProperty -name PackageName -Value $PackageName
    $configXMLdata | Add-Member -type NoteProperty -name FullLoad -Value $fullLoadCommand
    $configXMLdata | Add-Member -type NoteProperty -name SavePackageTo -Value $SavePackageTo
    $configXMLdata | Add-Member -type NoteProperty -name PVAD -Value $PVADCommand

    return $configXMLdata

}

function createPackage {

    if (test-path "$SavePackageTo\$packageName") {
        if (!($overwriteExistingPackage)) {
            $body = "The package already exists in the specified location {0}\{1} and `$overWriteExistingPackage was set to $overwriteExistingPackage, which prevented us from this continuing.  This sequence is halted." -f $SavePackageTo, $PackageName
            Send-MailMessage -To $sendReportTo -Subject "PREVIOUS PACKAGE DETECTED:$applicationName" -body $body -SmtpServer "mail.crha-health.ab.ca" -from "AppVSequencer@albertahealthservices.ca"
            exit
        } else {
            write-verbose -message "Removing previously detected package.  `$overwriteExistingPackage = $overwriteExistingPackage"
            rmdir "$SavePackageTo\$packageName" -Recurse -Force
        }
    }
    
    #create package
    cd $localSharePath



    write-verbose -message "Command: New-AppvSequencerPackage -Name $packageName -Path $SavePackageTo -Installer $installScript $PVAD $fullLoad -TemplateFilePath $templateFilePath"
    iex "New-AppvSequencerPackage -Name $packageName -Path $SavePackageTo -Installer $installScript $PVAD $fullLoad -TemplateFilePath $templateFilePath"


}

function produceReport {
    #delay 30 seconds to allow for the event log to write its events
    sleep 30
    #get the events 1 second before the sequence started
    $ISO8601StartDate = $PreSequenceTime.Add(-10000000)
    $ISO8601StartDate = $ISO8601StartDate.ToUniversalTime()
    $ISO8601StartDate = $ISO8601StartDate.ToString("s")
    write-verbose -message "ISO start time: $ISO8601StartDate"

    #get the events 10 seconds after the sequence ended
    $ISO8601EndDate = $PostSequenceTime.Add(100000000)
    $ISO8601EndDate = $ISO8601EndDate.ToUniversalTime()
    $ISO8601EndDate = $ISO8601EndDate.ToString("s")
    write-verbose -message "ISO end time: $ISO8601EndDate"

    #AppV Sequencer Event Log XPath scan
    $AppVXpath = @"
*[System[
TimeCreated[@SystemTime&gt;= '$ISO8601StartDate']
and TimeCreated[@SystemTime&lt;='$ISO8601EndDate']]]
"@
    $appVSeq_Events = Get-WinEvent -ProviderName 'Microsoft-AppV-Sequencer' -FilterXPath $AppVXpath -ErrorAction Stop

    #check to see if process creation/termination logging is enabled.  If not skip...
    $enableProcCreLogSearch = $false
    $auditProcCre = auditpol /get /subcategory:"Process Creation"
    if (!($auditProcCre | select-string "Success and Failure")) {
        if ($auditProcCre | select-string "Success") {
            $enableProcCreLogSearch = $true
        } else {
            write-verbose -message "Auditing of Process Creation Success is NOT enabled"
        }
        if ($auditProcCre | select-string "Failure") {
            $enableProcCreLogSearch = $true
        } else {
            write-verbose -message "Auditing of Process Creation Failure is NOT enabled"
        }
    } else {
        $enableProcCreLogSearch  = $true
    }

    $enableProcTermLogSearch  = $false
    $auditProcTermination = auditpol /get /subcategory:"Process Termination"
    if (!($auditProcTermination | select-string "Success and Failure")) {
        if ($auditProcTermination | select-string "Success") {
            $enableProcTermLogSearch = $true
        } else {
            write-verbose -message "Auditing of Process Termination Success is NOT enabled"
        }
        if ($auditProcTermination | select-string "Failure") {
            $enableProcTermLogSearch = $true
        } else {
            write-verbose -message "Auditing of Process Termination Failure is NOT enabled"
        }
    } else {
        $enableProcTermLogSearch  = $true
    }


    if ($enableProcCreLogSearch -eq $true) {
        write-verbose -message "Scanning event log for process creation events..."
        #processCreation XPath search string
        $AppVXpath = @"
*[System[(EventID='4688')
and TimeCreated[@SystemTime &gt; '$ISO8601StartDate']
and TimeCreated[@SystemTime &lt; '$ISO8601EndDate']]]
"@
        $appVSeq_Events += Get-WinEvent -ProviderName 'Microsoft-Windows-Security-Auditing' -FilterXPath $AppVXpath -ErrorAction Stop
    }

    if ($enableProcTermLogSearch -eq $true) {
        write-verbose -message "Scanning event log for process termination events..."
        #processTermination XPath search string
        $AppVXpath = @"
*[System[(EventID='4689')
and TimeCreated[@SystemTime &gt; '$ISO8601StartDate']
and TimeCreated[@SystemTime &lt; '$ISO8601EndDate']]]
"@
        $appVSeq_Events += Get-WinEvent -ProviderName 'Microsoft-Windows-Security-Auditing' -FilterXPath $AppVXpath -ErrorAction Stop
    }


    #this will format the results from the event log scans to something we can output, sort and read.
    $eventsArray = @()
    #we need to parse the raw event data for some values.  Convert them to XML and add them to an array to accomplish it...
    foreach ($event in $appVSeq_Events) {
        $events = New-Object System.Object
        [xml]$XMLEvent = $event.ToXML()
        $events | Add-Member -type NoteProperty -name TimeCreated -Value $event.TimeCreated
        $events | Add-Member -type NoteProperty -name Message -Value $event.Message
        if ($event.Message -match "exited") { 
            $events | Add-Member -type NoteProperty -name Message -Value "A process has exited..." -force
            $events | Add-Member -type NoteProperty -name ProcessName -Value $XMLEvent.Event.EventData.Data[6]."#text"
            $events | Add-Member -type NoteProperty -name ExitStatus -Value $XMLEvent.Event.EventData.Data[4]."#text"
        }
        if ($event.Message -match "created") {
            $events | Add-Member -type NoteProperty -name Message -Value "A new process has been created...." -force -ErrorAction SilentlyContinue
            $events | Add-Member -type NoteProperty -name ProcessName -Value $XMLEvent.Event.EventData.Data[5]."#text"
        }
        $eventsArray += $events
    }

    #tertiary information
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "$applicationName sequenced on $PreSequenceTime" -force
    $eventsArray += $events
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "Package completed at: $PostSequenceTime" -force
    $eventsArray += $events
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "Sequencer: $env:computername" -force
    $eventsArray += $events
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "Package Saved to: $savePackageTo" -force
    $eventsArray += $events
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "Sequenced by: $sendReportTo" -force
    $eventsArray += $events
    $events = New-Object System.Object
    $events | Add-Member -type NoteProperty -name Message -Value "Packaging duration: $durationOfSequence minutes" -force
    $eventsArray += $events

    #format for HTML output
    $a = "&lt;style&gt;"
    $a = $a + "BODY{background-color:white;}"
    $a = $a + "TABLE{border-width: 1px;border-style: solid;border-color: white;border-collapse: collapse;}"
    $a = $a + "TH{border-width: 3px;padding: 0px;border-style: solid;border-color: white;background-color:darkcyan}"
    $a = $a + "TD{border-width: 3px;padding: 0px;border-style: solid;border-color: white;background-color:lightcyan}"
    $a = $a + "&lt;/style&gt;"

    #save report with the package
    $eventsArray | select "TimeCreated","Message","ProcessName","ExitStatus" | sort "TimeCreated" | ConvertTo-HTML -head $a | Out-File "$SavePackageTo\$packageName\Report.htm"
    $body = $eventsArray | select "TimeCreated","Message","ProcessName","ExitStatus" | sort "TimeCreated" | ConvertTo-HTML -head $a

    Stop-Transcript
    copy "$env:TEMP\transcript.log" "$SavePackageTo\$packageName"

    #if the package exists send a report
    if (test-path "$SavePackageTo\$packageName\$packageName.appv") {
        Send-MailMessage -To $sendReportTo -Subject "AppV sequencer completed package:$applicationName" -body ($body | out-string) -BodyAsHtml -SmtpServer "mail.crha-health.ab.ca" -from "AppVSequencer@albertahealthservices.ca"
    }
}

#check for install files
monitorFiles

$configData = readConfigXML
#create variables
$fullLoad = $configData.FullLoad
$packageName =  $configData.packageName
$SavePackageTo =  $configData.SavePackageTo
$installScript =  $configData.installScript
$PVAD =  $configData.PVAD
$applicationName = $configData.applicationName
$sendReportTo = $configData.sendReportTo

$PreSequenceTime = get-date
write-verbose -message "Presequence Time: $($PreSequenceTime.ToShortTimeString())"
#create package
createPackage
$PostSequenceTime = get-date
write-verbose -message "Post sequence Time: $($PostSequenceTime.ToShortTimeString())"
$durationOfSequence = (New-TimeSpan -Start $PreSequenceTime -End $PostSequenceTime).TotalMinutes
write-verbose -message "Sequencing Duration: $durationOfSequence"

#produce and email a report
produceReport
</pre>
                                                                                            </td>
                                                                                          </tr>
                                                                                          
                                                                                          <tr>
                                                                                            <td width="36">
                                                                                              3
                                                                                            </td>
                                                                                            
                                                                                            <td width="603">
                                                                                              ¬ <img class="size-full wp-image-1936 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image013-1.png" alt="" width="548" height="256" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image013-1.png 548w, http://theorypc.ca/wp-content/uploads/2017/01/image013-1-300x140.png 300w" sizes="(max-width: 548px) 100vw, 548px" />
                                                                                            </td>
                                                                                          </tr>
                                                                                          
                                                                                          <tr>
                                                                                            <td width="36">
                                                                                              4
                                                                                            </td>
                                                                                            
                                                                                            <td width="603">
                                                                                              Open ‚ÄòTask Scheduler‚Äô</p> 
                                                                                              
                                                                                              <p>
                                                                                                <img class="size-full wp-image-1937 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image014.png" alt="" width="410" height="503" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image014.png 410w, http://theorypc.ca/wp-content/uploads/2017/01/image014-245x300.png 245w" sizes="(max-width: 410px) 100vw, 410px" /></td> </tr> 
                                                                                                
                                                                                                <tr>
                                                                                                  <td width="36">
                                                                                                    5
                                                                                                  </td>
                                                                                                  
                                                                                                  <td width="603">
                                                                                                    Right-click ‚ÄòTask Scheduler Library‚Äô and select ‚ÄòCreate Basic Task‚Ä¶‚Äô</p> 
                                                                                                    
                                                                                                    <p style="text-align: left;">
                                                                                                      <img class="size-full wp-image-1938" src="http://theorypc.ca/wp-content/uploads/2017/01/image015.png" alt="" width="309" height="291" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image015.png 309w, http://theorypc.ca/wp-content/uploads/2017/01/image015-300x283.png 300w" sizes="(max-width: 309px) 100vw, 309px" />
                                                                                                    </p>
                                                                                                  </td>
                                                                                                </tr>
                                                                                                
                                                                                                <tr>
                                                                                                  <td width="36">
                                                                                                    6
                                                                                                  </td>
                                                                                                  
                                                                                                  <td width="603">
                                                                                                    Name the Task ‚ÄòAutoSequencer‚Äô</p> 
                                                                                                    
                                                                                                    <p>
                                                                                                      <img class="size-full wp-image-1939 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image016.png" alt="" width="701" height="480" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image016.png 701w, http://theorypc.ca/wp-content/uploads/2017/01/image016-300x205.png 300w" sizes="(max-width: 701px) 100vw, 701px" /></td> </tr> 
                                                                                                      
                                                                                                      <tr>
                                                                                                        <td width="36">
                                                                                                          7
                                                                                                        </td>
                                                                                                        
                                                                                                        <td width="603">
                                                                                                          Set the task to start ‚ÄòWhen the computer starts‚Äô</p> 
                                                                                                          
                                                                                                          <p>
                                                                                                            <img class="size-full wp-image-1940 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image017.png" alt="" width="700" height="480" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image017.png 700w, http://theorypc.ca/wp-content/uploads/2017/01/image017-300x206.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></td> </tr> 
                                                                                                            
                                                                                                            <tr>
                                                                                                              <td width="36">
                                                                                                                8
                                                                                                              </td>
                                                                                                              
                                                                                                              <td width="603">
                                                                                                                Select ‚ÄòStart a program‚Äô and click ‚ÄòNext‚Äô</p> 
                                                                                                                
                                                                                                                <p>
                                                                                                                  <img class="size-full wp-image-1941 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image018.png" alt="" width="699" height="478" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image018.png 699w, http://theorypc.ca/wp-content/uploads/2017/01/image018-300x205.png 300w" sizes="(max-width: 699px) 100vw, 699px" /></td> </tr> 
                                                                                                                  
                                                                                                                  <tr>
                                                                                                                    <td width="36">
                                                                                                                      9
                                                                                                                    </td>
                                                                                                                    
                                                                                                                    <td width="603">
                                                                                                                      For ‚ÄòProgram/script‚Äô enter ‚Äúpowershell.exe‚Äù and for ‚ÄòAdd arguments (optional):‚Äô enter ‚Äú-executionpolicy bypass -file &#8220;C:\swinst\AutoSequencerScript\AppVAutoSequencer.ps1&#8243;‚Äù</p> 
                                                                                                                      
                                                                                                                      <p>
                                                                                                                        <img class="size-full wp-image-1942 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image019.png" alt="" width="697" height="477" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image019.png 697w, http://theorypc.ca/wp-content/uploads/2017/01/image019-300x205.png 300w" sizes="(max-width: 697px) 100vw, 697px" /></td> </tr> 
                                                                                                                        
                                                                                                                        <tr>
                                                                                                                          <td width="36">
                                                                                                                            10
                                                                                                                          </td>
                                                                                                                          
                                                                                                                          <td width="603">
                                                                                                                            Check ‚ÄòOpen the properties dialog for this task when I click Finish‚Äô and click ‚ÄòFinish‚Äô</p> 
                                                                                                                            
                                                                                                                            <p>
                                                                                                                              <img class="size-full wp-image-1943 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image020.png" alt="" width="697" height="479" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image020.png 697w, http://theorypc.ca/wp-content/uploads/2017/01/image020-300x206.png 300w" sizes="(max-width: 697px) 100vw, 697px" /></td> </tr> 
                                                                                                                              
                                                                                                                              <tr>
                                                                                                                                <td width="36">
                                                                                                                                  11
                                                                                                                                </td>
                                                                                                                                
                                                                                                                                <td width="603">
                                                                                                                                  Select ‚ÄòRun whether user is logged on or not‚Äô</p> 
                                                                                                                                  
                                                                                                                                  <p>
                                                                                                                                    <img class="size-full wp-image-1944 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image021.png" alt="" width="637" height="470" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image021.png 637w, http://theorypc.ca/wp-content/uploads/2017/01/image021-300x221.png 300w" sizes="(max-width: 637px) 100vw, 637px" /></td> </tr> 
                                                                                                                                    
                                                                                                                                    <tr>
                                                                                                                                      <td width="36">
                                                                                                                                        12
                                                                                                                                      </td>
                                                                                                                                      
                                                                                                                                      <td width="603">
                                                                                                                                        Click ‚ÄòChange User or Group‚Ä¶‚Äô and enter your service account</p> 
                                                                                                                                        
                                                                                                                                        <p>
                                                                                                                                          <img class="size-full wp-image-1945 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image022.png" alt="" width="464" height="243" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image022.png 464w, http://theorypc.ca/wp-content/uploads/2017/01/image022-300x157.png 300w" sizes="(max-width: 464px) 100vw, 464px" /></td> </tr> 
                                                                                                                                          
                                                                                                                                          <tr>
                                                                                                                                            <td style="text-align: left;" width="36">
                                                                                                                                              13
                                                                                                                                            </td>
                                                                                                                                            
                                                                                                                                            <td width="603">
                                                                                                                                              <p style="text-align: left;">
                                                                                                                                                Click ‚ÄòOK‚Äô
                                                                                                                                              </p>
                                                                                                                                              
                                                                                                                                              <p>
                                                                                                                                                <img class="size-full wp-image-1946 alignleft" src="http://theorypc.ca/wp-content/uploads/2017/01/image023.png" alt="" width="633" height="470" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image023.png 633w, http://theorypc.ca/wp-content/uploads/2017/01/image023-300x223.png 300w" sizes="(max-width: 633px) 100vw, 633px" /></td> </tr> </tbody> </table> 
                                                                                                                                                
                                                                                                                                                <h2 style="text-align: left;">
                                                                                                                                                  2.3¬ ¬ ¬ ¬ ¬ Modify AppVAutoSequencer script as needed
                                                                                                                                                </h2>
                                                                                                                                                
                                                                                                                                                <table class=" alignleft" width="638">
                                                                                                                                                  <tr>
                                                                                                                                                    <td width="36">
                                                                                                                                                    </td>
                                                                                                                                                    
                                                                                                                                                    <td width="603">
                                                                                                                                                      The following pre-requisites are required for this to work:</p> 
                                                                                                                                                      
                                                                                                                                                      <p>
                                                                                                                                                        &#8211;¬ ¬ ¬ ¬ ¬ ¬ Sequencer must be on a PVS Target Device
                                                                                                                                                      </p>
                                                                                                                                                      
                                                                                                                                                      <p>
                                                                                                                                                        &#8211;¬ ¬ ¬ ¬ ¬ ¬ AppVAutoSequencer.ps1 script present on system
                                                                                                                                                      </p>
                                                                                                                                                      
                                                                                                                                                      <p>
                                                                                                                                                        &#8211;¬ ¬ ¬ ¬ ¬ ¬ Shared folder for application install files
                                                                                                                                                      </p>
                                                                                                                                                      
                                                                                                                                                      <p>
                                                                                                                                                        &#8211;¬ ¬ ¬ ¬ ¬ ¬ (optional) Service Account for running scheduled task and permissions on destination share</td> </tr> 
                                                                                                                                                        
                                                                                                                                                        <tr>
                                                                                                                                                          <td width="36">
                                                                                                                                                            1
                                                                                                                                                          </td>
                                                                                                                                                          
                                                                                                                                                          <td width="603">
                                                                                                                                                            <strong><u>While in Private or Maintence mode on the AppV Sequencer vDisk</u>. </strong>Edit the ‚ÄòAppVAutoSequencer.ps1‚Äô script</p> 
                                                                                                                                                            
                                                                                                                                                            <p>
                                                                                                                                                              &nbsp;</td> </tr> 
                                                                                                                                                              
                                                                                                                                                              <tr>
                                                                                                                                                                <td width="36">
                                                                                                                                                                  2
                                                                                                                                                                </td>
                                                                                                                                                                
                                                                                                                                                                <td width="603">
                                                                                                                                                                  There are 3 variables that can be modified listed at the start of this script:<br /> #Variables
                                                                                                                                                                </td>
                                                                                                                                                              </tr></tbody> </table> 
                                                                                                                                                              
                                                                                                                                                              <pre class="lang:default decode:true ">$localSharePath = "C:\swinst\AutoSequence" #path to the shared folder local on this machine

$templateFilePath = "D:\Template.appvt" #path the AppV template file

$overwriteExistingPackage = $true¬† #if this is true then any package with the same name in the "save" location will be deleted</pre>
                                                                                                                                                              
                                                                                                                                                              <p style="text-align: left;">
                                                                                                                                                                ¬ 3$localSharePath should be the local path to the share specified in step 2.1.34$templateFilePath can be blank if you do not have a template, otherwise specify the full path here5$overwriteExistingPackage =$true if you want to overwrite a package with the same name in the destination location, $false if you want it to simply fail to copy to the destination if a package already exists
                                                                                                                                                              </p>
                                                                                                                                                              
                                                                                                                                                              <h1 style="text-align: left;">
                                                                                                                                                                3¬ ¬ ¬ ¬ ¬ Save and promote your vDisk to Production
                                                                                                                                                              </h1>
                                                                                                                                                              
                                                                                                                                                              <table class=" alignleft" width="638">
                                                                                                                                                                <tr>
                                                                                                                                                                  <td width="36">
                                                                                                                                                                    1
                                                                                                                                                                  </td>
                                                                                                                                                                  
                                                                                                                                                                  <td width="603">
                                                                                                                                                                    ¬ <img class="aligncenter size-full wp-image-1947" src="http://theorypc.ca/wp-content/uploads/2017/01/image024.png" alt="" width="411" height="84" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image024.png 411w, http://theorypc.ca/wp-content/uploads/2017/01/image024-300x61.png 300w" sizes="(max-width: 411px) 100vw, 411px" />
                                                                                                                                                                  </td>
                                                                                                                                                                </tr>
                                                                                                                                                                
                                                                                                                                                                <tr>
                                                                                                                                                                  <td width="36">
                                                                                                                                                                    2
                                                                                                                                                                  </td>
                                                                                                                                                                  
                                                                                                                                                                  <td width="603">
                                                                                                                                                                    Click ‚ÄòPromote‚Ä¶‚Äô</p> 
                                                                                                                                                                    
                                                                                                                                                                    <p>
                                                                                                                                                                      <img class="aligncenter size-full wp-image-1948" src="http://theorypc.ca/wp-content/uploads/2017/01/image025.png" alt="" width="655" height="416" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image025.png 655w, http://theorypc.ca/wp-content/uploads/2017/01/image025-300x191.png 300w" sizes="(max-width: 655px) 100vw, 655px" /></td> </tr> 
                                                                                                                                                                      
                                                                                                                                                                      <tr>
                                                                                                                                                                        <td style="text-align: left;" width="36">
                                                                                                                                                                          3
                                                                                                                                                                        </td>
                                                                                                                                                                        
                                                                                                                                                                        <td width="603">
                                                                                                                                                                          <p style="text-align: left;">
                                                                                                                                                                            Click ‚ÄòOK‚Äô
                                                                                                                                                                          </p>
                                                                                                                                                                          
                                                                                                                                                                          <p>
                                                                                                                                                                            <img class="aligncenter size-full wp-image-1949" src="http://theorypc.ca/wp-content/uploads/2017/01/image026.png" alt="" width="536" height="244" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image026.png 536w, http://theorypc.ca/wp-content/uploads/2017/01/image026-300x137.png 300w" sizes="(max-width: 536px) 100vw, 536px" /></td> </tr> </tbody> </table> 
                                                                                                                                                                            
                                                                                                                                                                            <h1 style="text-align: left;">
                                                                                                                                                                              4¬ ¬ ¬ ¬ ¬ Using the AppVAutoSequencer
                                                                                                                                                                            </h1>
                                                                                                                                                                            
                                                                                                                                                                            <table class=" alignleft" width="638">
                                                                                                                                                                              <tr>
                                                                                                                                                                                <td width="36">
                                                                                                                                                                                  1
                                                                                                                                                                                </td>
                                                                                                                                                                                
                                                                                                                                                                                <td width="603">
                                                                                                                                                                                  The AppVAutoSequencer is now configured.¬ The AutoSequencer has some requirements:</p> 
                                                                                                                                                                                  
                                                                                                                                                                                  <p>
                                                                                                                                                                                    &#8211;¬ ¬ ¬ ¬ ¬ ¬ A silent install script must exist
                                                                                                                                                                                  </p>
                                                                                                                                                                                  
                                                                                                                                                                                  <p>
                                                                                                                                                                                    &#8211;¬ ¬ ¬ ¬ ¬ ¬ A Config.xml file must exist with the install files that specify some additional parameters for the package</td> </tr> 
                                                                                                                                                                                    
                                                                                                                                                                                    <tr>
                                                                                                                                                                                      <td width="36">
                                                                                                                                                                                        2
                                                                                                                                                                                      </td>
                                                                                                                                                                                      
                                                                                                                                                                                      <td width="603">
                                                                                                                                                                                        The config.xml file that comes with the package should be in the following format:</p> 
                                                                                                                                                                                        
                                                                                                                                                                                        <pre class="lang:xhtml decode:true">&lt;?xml version="1.0"?&gt;
&lt;Settings&gt;
 &lt;Application&gt;
  &lt;ApplicationName&gt;VMWare_vSphereClient_6.0.0_x86&lt;/ApplicationName&gt;
  &lt;SendReportTo&gt;trentent.tye2@albertahealthservices.ca&lt;/SendReportTo&gt;
  &lt;verboseReport&gt;true&lt;/verboseReport&gt;
  &lt;installScript&gt;install.cmd&lt;/installScript&gt;
  &lt;PackageName&gt;VMWare_vSphereClient_6.0.0_x86&lt;/PackageName&gt;
  &lt;FullLoad&gt;true&lt;/FullLoad&gt;
  &lt;SavePackageTo&gt;\\fileshare.ca\appv_images_bdc\AppV5\AutoGeneratedPackages&lt;/SavePackageTo&gt;
  &lt;PVAD&gt;C:\application&lt;/PVAD&gt;
 &lt;/Application&gt;
&lt;/Settings&gt;</pre>
                                                                                                                                                                                      </td>
                                                                                                                                                                                    </tr>
                                                                                                                                                                                    
                                                                                                                                                                                    <tr>
                                                                                                                                                                                      <td width="36">
                                                                                                                                                                                        3
                                                                                                                                                                                      </td>
                                                                                                                                                                                      
                                                                                                                                                                                      <td width="603">
                                                                                                                                                                                        The options to customize are:</p> 
                                                                                                                                                                                        
                                                                                                                                                                                        <p>
                                                                                                                                                                                          ApplicationName: Name of the application you are sequencing</td> </tr> 
                                                                                                                                                                                          
                                                                                                                                                                                          <tr>
                                                                                                                                                                                            <td width="36">
                                                                                                                                                                                            </td>
                                                                                                                                                                                            
                                                                                                                                                                                            <td width="603">
                                                                                                                                                                                              Send Report: email address of person to receive the report
                                                                                                                                                                                            </td>
                                                                                                                                                                                          </tr>
                                                                                                                                                                                          
                                                                                                                                                                                          <tr>
                                                                                                                                                                                            <td width="36">
                                                                                                                                                                                            </td>
                                                                                                                                                                                            
                                                                                                                                                                                            <td width="603">
                                                                                                                                                                                              versboseReport: whether to provide verbose install output
                                                                                                                                                                                            </td>
                                                                                                                                                                                          </tr>
                                                                                                                                                                                          
                                                                                                                                                                                          <tr>
                                                                                                                                                                                            <td width="36">
                                                                                                                                                                                            </td>
                                                                                                                                                                                            
                                                                                                                                                                                            <td width="603">
                                                                                                                                                                                              installScript: script file with the silent install options
                                                                                                                                                                                            </td>
                                                                                                                                                                                          </tr>
                                                                                                                                                                                          
                                                                                                                                                                                          <tr>
                                                                                                                                                                                            <td width="36">
                                                                                                                                                                                            </td>
                                                                                                                                                                                            
                                                                                                                                                                                            <td width="603">
                                                                                                                                                                                              SavePackageTo: location to put the completed package
                                                                                                                                                                                            </td>
                                                                                                                                                                                          </tr>
                                                                                                                                                                                          
                                                                                                                                                                                          <tr>
                                                                                                                                                                                            <td style="text-align: left;" width="36">
                                                                                                                                                                                            </td>
                                                                                                                                                                                            
                                                                                                                                                                                            <td style="text-align: left;" width="603">
                                                                                                                                                                                              PVAD: if you require a PVAD enter it here
                                                                                                                                                                                            </td>
                                                                                                                                                                                          </tr></tbody> </table> 
                                                                                                                                                                                          
                                                                                                                                                                                          <h2 style="text-align: left;">
                                                                                                                                                                                            4.1¬ ¬ ¬ ¬ ¬ Example package ‚Äì AGFA IMPAX 6.6
                                                                                                                                                                                          </h2>
                                                                                                                                                                                          
                                                                                                                                                                                          <table class=" alignleft" width="638">
                                                                                                                                                                                            <tr>
                                                                                                                                                                                              <td width="36">
                                                                                                                                                                                                1
                                                                                                                                                                                              </td>
                                                                                                                                                                                              
                                                                                                                                                                                              <td width="603">
                                                                                                                                                                                                Files:</p> 
                                                                                                                                                                                                
                                                                                                                                                                                                <p>
                                                                                                                                                                                                  <img class="aligncenter size-full wp-image-1950" src="http://theorypc.ca/wp-content/uploads/2017/01/image027.png" alt="" width="483" height="303" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image027.png 483w, http://theorypc.ca/wp-content/uploads/2017/01/image027-300x188.png 300w" sizes="(max-width: 483px) 100vw, 483px" /></td> </tr> 
                                                                                                                                                                                                  
                                                                                                                                                                                                  <tr>
                                                                                                                                                                                                    <td width="36">
                                                                                                                                                                                                      2
                                                                                                                                                                                                    </td>
                                                                                                                                                                                                    
                                                                                                                                                                                                    <td width="603">
                                                                                                                                                                                                      The config.xml file:</p> 
                                                                                                                                                                                                      
                                                                                                                                                                                                      <p>
                                                                                                                                                                                                        <img class="aligncenter size-full wp-image-1951" src="http://theorypc.ca/wp-content/uploads/2017/01/image028.png" alt="" width="708" height="311" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image028.png 708w, http://theorypc.ca/wp-content/uploads/2017/01/image028-300x132.png 300w" sizes="(max-width: 708px) 100vw, 708px" /></td> </tr> 
                                                                                                                                                                                                        
                                                                                                                                                                                                        <tr>
                                                                                                                                                                                                          <td style="text-align: left;" width="36">
                                                                                                                                                                                                            3
                                                                                                                                                                                                          </td>
                                                                                                                                                                                                          
                                                                                                                                                                                                          <td width="603">
                                                                                                                                                                                                            <p style="text-align: left;">
                                                                                                                                                                                                              Install.cmd file:
                                                                                                                                                                                                            </p>
                                                                                                                                                                                                            
                                                                                                                                                                                                            <p>
                                                                                                                                                                                                              <img class="aligncenter size-full wp-image-1952" src="http://theorypc.ca/wp-content/uploads/2017/01/image029.png" alt="" width="903" height="418" srcset="http://theorypc.ca/wp-content/uploads/2017/01/image029.png 903w, http://theorypc.ca/wp-content/uploads/2017/01/image029-300x139.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/image029-768x356.png 768w" sizes="(max-width: 903px) 100vw, 903px" /></td> </tr> </tbody> </table> 
                                                                                                                                                                                                              
                                                                                                                                                                                                              <p style="text-align: left;">
                                                                                                                                                                                                                Video of the auto-sequencer in action:
                                                                                                                                                                                                              </p>
                                                                                                                                                                                                              
                                                                                                                                                                                                              <div style="width: 720px;" class="wp-video">
                                                                                                                                                                                                                <video class="wp-video-shortcode" id="video-1922-4" width="720" height="664" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/AutomatedSequencer_example.mp4?_=4" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/AutomatedSequencer_example.mp4">http://theorypc.ca/wp-content/uploads/2017/01/AutomatedSequencer_example.mp4</a></video>
                                                                                                                                                                                                              </div>
                                                                                                                                                                                                              
                                                                                                                                                                                                              <p style="text-align: left;">
                                                                                                                                                                                                                An example of the report it spits out to you:
                                                                                                                                                                                                              </p>
                                                                                                                                                                                                              
                                                                                                                                                                                                              <table class=" alignleft">
                                                                                                                                                                                                                <colgroup> <col /> <col /> <col /> <col /></colgroup> <tr>
                                                                                                                                                                                                                  <th>
                                                                                                                                                                                                                    TimeCreated
                                                                                                                                                                                                                  </th>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <th>
                                                                                                                                                                                                                    Message
                                                                                                                                                                                                                  </th>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <th>
                                                                                                                                                                                                                    ProcessName
                                                                                                                                                                                                                  </th>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <th>
                                                                                                                                                                                                                    ExitStatus
                                                                                                                                                                                                                  </th>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Sequencer: WSAPVSEQ07
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Package completed at: 01/05/2017 15:40:53
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    AGFA_Impax_6.6.1_x86 sequenced on 01/05/2017 15:37:27
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Packaging duration: 3.42796070166667 minutes
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Sequenced by: trentent.tye2@albertahealthservices.ca
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Package Saved to: \\fileshare.ca\appv_images_bdc\AppV5\AutoGeneratedPackages
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:31 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following service is being stopped and disabled for this monitoring session. Service Name: wuauserv.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:31 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Monitoring driver loaded.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:31 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry minifilter loaded.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:31 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:31 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files\Microsoft Application Virtualization\Sequencer\AppVSequencer\VAppCollector.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files\Microsoft Application Virtualization\Sequencer\AppVSequencer\VAppCollector.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\cmd.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\swinst\AutoSequence\IMPAXClientSetup.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:34 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Users\svc_apvinstall\AppData\Local\Temp\{B1DF8D53-D48D-43B3-8F58-B29046FE3F20}\{270b0954-35ca-4324-bbc6-ba5db9072dad}\vcredist_x86.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:36 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    D:\c775c2a4ff3c59c7fc3d8ad5ba64313b\Setup.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:43 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:44 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:46 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    D:\c775c2a4ff3c59c7fc3d8ad5ba64313b\Setup.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:46 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Users\svc_apvinstall\AppData\Local\Temp\{B1DF8D53-D48D-43B3-8F58-B29046FE3F20}\{270b0954-35ca-4324-bbc6-ba5db9072dad}\vcredist_x86.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:50 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:50 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:50 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\Installer\MSI5C86.tmp
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\Installer\MSI5C86.tmp
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\UI0Detect.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:52 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files (x86)\Agfa\IMPAX Client\ClientActions.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:52 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files (x86)\Agfa\IMPAX Client\Agfa.Client.Updater.Service.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\regsvr32.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\regsvr32.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files (x86)\Agfa\IMPAX Client\ClientActions.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\Installer\MSI65AB.tmp
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\Installer\MSI65AB.tmp
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files (x86)\Agfa\IMPAX Client\mitra_context_server.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files (x86)\Agfa\IMPAX Client\mitra_context_server.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:53 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\SysWOW64\msiexec.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:57 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\swinst\AutoSequence\IMPAXClientSetup.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:57 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\xcopy.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:57 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\xcopy.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:57 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\cmd.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:37:57 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\sc.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\taskhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\rundll32.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\taskhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:05 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\wsqmcons.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\sc.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x420
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\rundll32.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\schtasks.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\schtasks.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:38:06 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\wsqmcons.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x80001101
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:39:30 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\wbem\WmiApSrv.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Monitoring driver unloaded.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry minifilter unloaded.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry Monitoring Subsystem starts responding to MonitoringStopped event. Pausing registry monitoring and writing registry to Ingredients.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:32 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry monitor log lost 0 event(s). If more than zero events were lost the package may be invalid.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:33 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry Monitoring Subsystem stops responding to MonitoringStopped event. Resuming monitoring registry changes.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:33 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Monitoring: File monitor log lost 0 event(s). If more than zero events were lost the package may be invalid.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:34 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:34 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files\Microsoft Application Virtualization\Sequencer\AppVSequencer\VAppCollector.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:34 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Program Files\Microsoft Application Virtualization\Sequencer\AppVSequencer\VAppCollector.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:34 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A process has exited&#8230;
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    C:\Windows\System32\conhost.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    0x0
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SECURITY
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles\Scm\0b916caa-294a-4ed5-814c-d50d0ab90bdd
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SECURITY.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SOFTWARE
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SOFTWARE.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\DEFAULT
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\DEFAULT.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\System Volume Information\Syscache.hve
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\System Volume Information\Syscache.hve.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles\Scm\b186343a-1f0d-4d01-a03b-a34830ce1fa8
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles\Scm\7debb1c0-bb8b-4f02-8d74-38377ad04c69
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles\Scm\ddb62c95-d63d-4a59-b7c5-224fa446a980
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SYSTEM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\NTUSER.DAT
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\ntuser.dat.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$\Managed\1D5E3C0FEDA1E123187686FED06E995A\10.0.40219\F_CENTRAL_msvcp100_x86
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\SourceHash{F0C3E5D1-1ADE-321E-8167-68EF0DE699A5}
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Microsoft\Windows\UsrClass.dat
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Temp\Microsoft Visual C++ 2010 x86 Redistributable Setup_20170105_153739402-MSI_vc_red.msi.txt
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Temp\setperm.log
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Microsoft\Windows\UsrClass.dat.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Temp\Microsoft Visual C++ 2010 x86 Redistributable Setup_20170105_153739402.html
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\65a65.msi
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\{7B4200BE-28A2-4063-B240-9F7EEAFBD9C0}\StartMenuImpaxClient_A4DAE8516C0B4BAAB4BB987376837732.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\65a66.mst
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config\SYSTEM.LOG1
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\{7B4200BE-28A2-4063-B240-9F7EEAFBD9C0}\DesktopImpaxClientSh_A4DAE8516C0B4BAAB4BB987376837732.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$\Managed\1D5E3C0FEDA1E123187686FED06E995A\10.0.40219\F_CENTRAL_msvcr100_x86
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\63df0.msi
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\{7B4200BE-28A2-4063-B240-9F7EEAFBD9C0}\ARPPRODUCTICON.exe
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\SourceHash{7B4200BE-28A2-4063-B240-9F7EEAFBD9C0}
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\config
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles\Scm
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\LocalService
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\System Volume Information
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\System32\LogFiles
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\ServiceProfiles\NetworkService
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$\Managed\1D5E3C0FEDA1E123187686FED06E995A\10.0.40219
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$\Managed\1D5E3C0FEDA1E123187686FED06E995A
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Temp\Microsoft Visual C++ 2010 x86 Redistributable Setup_10.0.40219
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\{7B4200BE-28A2-4063-B240-9F7EEAFBD9C0}
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer\$PatchCache$\Managed
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Windows\Installer
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The following file was excluded from packaging: C:\Users\svc_apvinstall\AppData\Local\Temp
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:35 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:36 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    A new process has been created&#8230;.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:44 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Virtual Font Packaging Subsystem is responding to PrePackaging event. Collecting virtual fonts and writing them to the manifest.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:44 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Virtual Font Packaging Subsystem has finished collecting and writing all fonts to the manifest.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The Visual C++ Runtime policy.9.0.Microsoft.VC90.CRT version 9.0.30729.6161 (x86) was detected and packaged.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The Visual C++ Runtime Microsoft.VC90.CRT version 9.0.30729.6161 (x86) was detected and packaged.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Filesystem Metadata Packaging Subsystem stops responding to PackagingStarting event. Finished writing installation path to FileSystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Filesystem Metadata Packaging Subsystem started responding to PackagingStarting event. Writing installation path to FilesystemMetadata. Install Path: C:\f1d8b1e7-9cdb-41d3-8a24-cb821d59f375.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The Static Dependency Packaging Subsystem is starting to respond to the event PackagingStarting. Static dll dependency list is being built.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Scripts and related files are being added to the publishing feature block.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Icons are being added to the publishing feature block.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The static dependencies section is being written to the FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:45 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The Static Dependency Packaging Subsystem has finished responding to the event PackagingStarting. Static dll dependency list has been built and added to FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:46 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry Packaging Subsystem starts responding to PackagingStarted event. Virtual package registry hive is being written to the package.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:46 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Registry Packaging Subsystem stops responding to PackagingStarted event. Virtual package registry hive has been written to the package.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:48 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The file &#8216;C:\Users\Public\Impax\Logs\ClientUpdater.log&#8217; could not be added to the package (The process cannot access the file &#8216;C:\Users\Public\Impax\Logs\ClientUpdater.log&#8217; because it is being used by another process.).
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The filesystem section is being written to the FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Short Name Packaging Subystem has started adding shortnames to FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Directory Metadata Packaging Subsystem started responding to PackagingStarted event. Classifying empty and opaque directories.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Short Name Packaging Subystem has finished adding shortnames to FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The opaque directories section is being written to the FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Filesystem Metadata Packaging Subsystem starts responding to PackagingStarted event. Adding FilesystemMetadata.xml to the package.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Filesystem Metadata Packaging Subsystem stops responding to PackagingStarted event. FilesystemMetadata.xml added to the package.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    The empty directories section is being written to the FilesystemMetadata.xml.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                                
                                                                                                                                                                                                                <tr>
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    1/5/2017 3:40:51 PM
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                    Directory Metadata Packaging Subsystem has finished responding to the PackagingStarted event. Empty and opaque directories have been classified.
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                  
                                                                                                                                                                                                                  <td>
                                                                                                                                                                                                                  </td>
                                                                                                                                                                                                                </tr>
                                                                                                                                                                                                              </table>
                                                                                                                                                                                                              
                                                                                                                                                                                                              <!-- AddThis Advanced Settings generic via filter on the_content -->
                                                                                                                                                                                                              
                                                                                                                                                                                                              <!-- AddThis Share Buttons generic via filter on the_content -->