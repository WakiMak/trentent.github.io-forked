---
id: 620
title: Slow PowerShell CSV reading and object generation
date: 2013-12-17T10:22:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/12/17/slow-powershell-csv-reading-and-object-generation/
permalink: /2013/12/17/slow-powershell-csv-reading-and-object-generation/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/12/slow-powershell-csv-reading-and-object.html
blogger_internal:
  - /feeds/7977773/posts/default/525868472017919553
categories:
  - Blog
  - Uncategorized
tags:
  - Performance
  - PowerShell
  - scripting
---
I am attempting to read a CSV file and decided the best way to do it was with PowerShell's native import-csv tool.Â What was required was to read this CSV and then generate a registry file for import into numerous computers.Â This was my result:

<pre class="lang:ps decode:true ">########################################################################################
#
#  Created by Trentent Tye
#             IBM Intel Server Team
#             December 13, 2013
#
# Description: This script will take a CSV file that contains values that are flexible
# and maintained by the MetaVision team and create a .reg file and import that file
# into the computer it's run on.
#########################################################################################

Get-Date
$csv = Import-csv .\MetaVision.csv

$output = "Windows Registry Editor Version 5.00`n`n"

$date = get-date
write-host first run $date
Foreach ($server in $csv.Server) { 
$regObject = $csv | Where {$_.server -eq $Server}
$output += "[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\iMD Soft\Citrix Clients\$server\Database Connect]`n"
$output += """Default Offline Mode""=""0""`n"
$output += """Default  Offline Mode""=""0""`n"
$output += """Domain Department""=""{0}""`n" -f $regObject.'Domain Department'
$output += """Default Department""=""{0}""`n" -f $regObject.'Default Department'
$output += """EMPI Database""=""{0}""`n" -f $regObject.'EMPI Database'
$output += """EMPI Server""=""{0}""`n" -f $regObject.'EMPI Server'
$output += """Offline Mode""=""0""`n"
$output += """Production Database""=""{0}""`n" -f $regObject.'Production Database'
$output += """Production Server""=""{0}""`n`n" -f $regObject.'Production Server'
$output += "[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\iMD Soft\Citrix Clients\$server\Settings]`n"
$output += """Application Size""=""1""`n"
$output += """Customize Units""=""0""`n"
$output += """DebugMode""=""0""`n"
$output += """DisplayLocalizationID""=""0""`n"
$output += """LastLogin""=""null""`n"
$output += """LocaleID""=""1033""`n"
$output += """LogFileMode""=""1""`n"
$output += """LogFilePath""=""""`n"
$output += """LoginPolicyMode""=""{0}""`n" -f $regObject.'LoginPolicyMode'
$output += """ProximityLockRange""=""0""`n"
$output += """ProximityObject""=""""`n"
$output += """ProximityUnlockRange""=""0""`n"
$output += """Silent""=""1""`n"
$output += """UseMRDDriver""=""0""`n"
$output += """UseVirtualKeyboard""=""0""`n"
$output += """Workstation BedID""=""{0}""`n" -f $regObject.'Workstation BedID'
$output += """Workstation LayoutID""=""""`n"
$output += """Workstation Type""=""{0}""`n" -f $regObject.'Workstation Type'
$output += """WriteActionsToLog""=""0""`n`n"
}

$date2 = get-date
$totaltime = ($date2 - $date).TotalSeconds / 60
write-host first complete $totaltime


rm "$env:temp\MetaReg.reg"
write-output $output | out-file -FilePath "$env:temp\MetaReg.reg"
Get-Date
write-host "Importing registry"
regedit /s "$env:temp\MetaReg.reg"
Get-Date</pre>

The CSV file we have has about 1500 lines in it.Â To generate the registry key utilizing this method took 6 minutes and 45 seconds.Â This is unacceptably slow.Â I then started googling ways to speed up this processing and came across this article:  
<http://stackoverflow.com/questions/6386793/how-to-use-powershell-to-reorder-csv-columns>

Where Roman Kuzmin suggested to handle the file as a text file instead of a PowerShell object.Â The syntax used to generate convert the file into objects that can replace text as needed is a bit different but I decided to explore it.Â His example code is as follows:

<pre class="lang:ps decode:true ">$reader = [System.IO.File]::OpenText('data1.csv')
$writer = New-Object System.IO.StreamWriter 'data2.csv'
for(;;) {
    $line = $reader.ReadLine()
    if ($null -eq $line) {
        break
    }
    $data = $line.Split(";")
    $writer.WriteLine('{0};{1};{2}', $data[0], $data[2], $data[1])
}
$reader.Close()
$writer.Close()
</pre>

Essentially, he is proposing reading the file line by line and extracting the data by manually splitting the text string. The string then turns into an array that you can use for substitution. This is my final code using his example:

<pre class="lang:ps decode:true ">########################################################################################
#
#  Created by Trentent Tye
#             IBM Intel Server Team
#             December 13, 2013
#
# Description: This script will take a CSV file that contains values that are flexible
# and maintained by the MetaVision team and create a .reg file and import that file
# into the computer it's run on.
#########################################################################################


$date = get-date
write-host first run $date
if (test-Path $env:temp\MetaReg.reg) {rm "$env:temp\MetaReg.reg"}


$reader = [System.IO.File]::OpenText('MetaVision.csv')
$reader.ReadLine() # skip first line
$writer = New-Object System.IO.StreamWriter "$env:temp\MetaReg.reg"
$writer.WriteLine("Windows Registry Editor Version 5.00")
$writer.WriteLine("")

for(;;) {
    $line = $reader.ReadLine()
    if ($null -eq $line) {
        break
    }
    $data = $line.Split(",")
    $writer.WriteLine("[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\iMD Soft\Citrix Clients\{0}\Database Connect]`n", $data[0])
    $writer.WriteLine("""Default Offline Mode""=""0""`n")
    $writer.WriteLine("""Default  Offline Mode""=""0""`n")
    $writer.WriteLine("""Domain Department""=""{0}""`n", $data[2])
    $writer.WriteLine("""Default Department""=""{0}""`n", $data[1])
    $writer.WriteLine("""EMPI Database""=""{0}""`n", $data[3])
    $writer.WriteLine("""EMPI Server""=""{0}""`n", $data[4])
    $writer.WriteLine("""Offline Mode""=""0""`n")
    $writer.WriteLine("""Production Database""=""{0}""`n", $data[5])
    $writer.WriteLine("""Production Server""=""{0}""`n`n", $data[6])
    $writer.WriteLine("")
    $writer.WriteLine("[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\iMD Soft\Citrix Clients\{0}\Settings]`n", $data[0])
    $writer.WriteLine("""Application Size""=""1""`n")
    $writer.WriteLine("""Customize Units""=""0""`n")
    $writer.WriteLine("""DebugMode""=""0""`n")
    $writer.WriteLine("""DisplayLocalizationID""=""0""`n")
    $writer.WriteLine("""LastLogin""=""null""`n")
    $writer.WriteLine("""LocaleID""=""1033""`n")
    $writer.WriteLine("""LogFileMode""=""1""`n")
    $writer.WriteLine("""LogFilePath""=""""`n")
    $writer.WriteLine("""LoginPolicyMode""=""{0}""`n", $data[8])
    $writer.WriteLine("""ProximityLockRange""=""0""`n")
    $writer.WriteLine("""ProximityObject""=""""`n")
    $writer.WriteLine("""ProximityUnlockRange""=""0""`n")
    $writer.WriteLine("""Silent""=""1""`n")
    $writer.WriteLine("""UseMRDDriver""=""0""`n")
    $writer.WriteLine("""UseVirtualKeyboard""=""0""`n")
    $writer.WriteLine("""Workstation BedID""=""{0}""`n", $data[9])
    $writer.WriteLine("""Workstation LayoutID""=""""`n")
    $writer.WriteLine("""Workstation Type""=""{0}""`n", $data[10])
    $writer.WriteLine("""WriteActionsToLog""=""0""`n`n")
    $writer.WriteLine("")
}
$reader.Close()
$writer.Close()



$date2 = get-date
$totaltime = ($date2 - $date).TotalSeconds / 60
write-host first complete $totaltime



write-host "Importing registry"
regedit /s "$env:temp\MetaReg.reg"
Get-Date
</pre>

&nbsp;

The total time for this? 0.07 seconds. Incredibly fast. So, utilize the Import-CSV command and object based creation with caution. Utilizing even a moderately sized file will take an unacceptable amount of time.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->