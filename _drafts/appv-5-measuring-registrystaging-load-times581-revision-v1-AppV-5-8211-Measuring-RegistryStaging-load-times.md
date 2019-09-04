---
id: 1025
title: 'AppV 5 &#8211; Measuring RegistryStaging load times'
date: 2016-04-24T19:54:44-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/581-revision-v1/
permalink: /2016/04/24/581-revision-v1/
---
Per [my previous posting](http://trentent.blogspot.ca/2014/11/appv-5-first-launch-application.html), I have an issue where the AppVClient.exe consumes significant CPU resources upon application launch. Â From a [Microsoft forum where another member](https://social.technet.microsoft.com/Forums/en-US/44944302-d8f3-4df1-b104-9c63345f88e0/poor-first-launch-performance-with-appv-5?forum=mdopappv) did some further investigation, he discovered that the slowness and delayed launch times are related to registry staging. Â To confirm and measure the impact of registry staging I wrote a script that measures the length of time it takes to finish registry staging for all AppV5 applications on your computer/server.

<pre class="lang:ps decode:true ">#import AppVClient module
ipmo *appv*
 
#get all appv packages
$apps = Get-AppvClientPackage
$obj = @()
 
#Get connection group applications
$connApps = (Get-AppvClientConnectionGroup).GetPackages()
 
#remove connecion-group applications from the list
[System.Collections.ArrayList]$appList = $apps
foreach ($connApp in $connApps) {$appList = $appList | ? { $_.name -ne $connApp.Name }}
 
 
#for each (non-connection group) appv package...
foreach ($app in $appList) {
$prop = New-Object System.Object
 
#get each appv package ID and version ID
$package = ($app.packageID).ToString()
$version = ($app.versionID).ToString()
 
#start a blank cmd.exe in the environment (this kicks off the AppV5 registry staging)
Start-AppvVirtualProcess -AppvClientObject (Get-AppvClientPackage $app.name) cmd.exe
 
#measures the length of time it takes before registry staging is finished (resolution is 1s)
$command = measure-command {
    do {
    write-host "Testing: " $app.name
        sleep 1
       }
       until (Test-Path "HKLM:\SOFTWARE\Microsoft\AppV\Client\Packages\$package\Versions\$version\RegistryStagingFinished")
       write-host $app.name
       $appvProcess = get-appvVirtualProcess
       stop-process $appvProcess -force
    }
    $prop | Add-Member -type NoteProperty -name Package -value $app.Name
    $prop | Add-Member -type NoteProperty -name RegistryStagingTime -value $command.TotalSeconds
    $obj += $prop
}
$obj | export-csv RegistryStagingTime.csv -NoTypeInformation
 
#for each connection group package...
$conGroups = Get-AppvClientConnectionGroup
foreach ($group in $conGroups) {
$prop = New-Object System.Object
 
#get each connection group package ID and version ID
$package = ($group.groupID).ToString()
$version = ($group.versionID).ToString()
 
#start a blank cmd.exe in the environment (this kicks off the AppV5 registry staging)
Start-AppvVirtualProcess -AppvClientObject ($group) cmd.exe
 
#measures the length of time it takes before registry staging is finished (resolution is 1s)
$command = measure-command {
    do {
    write-host "Testing: " $group.name
        sleep 1
       }
       until (Test-Path "HKLM:\SOFTWARE\Microsoft\AppV\Client\PackageGroups\$package\Versions\$version\RegistryStagingFinished")
       write-host $group.name
       $appvProcess = get-appvVirtualProcess
       stop-process $appvProcess -force
    }
    $prop | Add-Member -type NoteProperty -name Package -value $group.Name
    $prop | Add-Member -type NoteProperty -name RegistryStagingTime -value $command.TotalSeconds
    $obj += $prop
}
 
#exports all information to csv file
$obj | export-csv RegistryStagingTime.csv -NoTypeInformation</pre>

What this script does is iterate through all your AppV5 applications and then loads a cmd.exe with the AppV environment. Â It then checks for the RegistryStagingFinished registry key, and once it is found, it moves on to the next program. Â It records all this information than exports it as a CSV file.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-6ObTCrS4ujA/VI5Pg-03DQI/AAAAAAAAAsw/OuMGFSQ0ciY/s1600/AppV5-RegistryStagingTimes.png"><img src="http://4.bp.blogspot.com/-6ObTCrS4ujA/VI5Pg-03DQI/AAAAAAAAAsw/OuMGFSQ0ciY/s1600/AppV5-RegistryStagingTimes.png" width="361" height="400" border="0" /></a>
</div>

By utilizing this script as a AppV prelaunch/startup script we can optimize our users first application startup times and reduce CPU utilization of first-run applications.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->