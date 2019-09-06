---
id: 1694
title: ControlUp - Dissecting Logon Times a step further (invalid Home Directory impact)
date: 2016-09-07T21:00:47-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1694
permalink: /2016/09/07/controlup-dissecting-logon-times-a-step-further-invalid-home-directory-impact/
categories:
  - Blog
tags:
  - Citrix
  - ControlUp
  - Logons
  - Performance
  - PowerShell
  - scripting
  - &
---
Continuing on from my [previous post](http://theorypc.ca/2016/08/31/controlup-dissecting-logon-times-a-step-further-printer-loading/), we were still having certain users with logons in the dozens of seconds to minutes. Â I wanted to find out why and see if there is anything further that could be done.

<img class="aligncenter size-full wp-image-1698" src="http://theorypc.ca/wp-content/uploads/2016/09/60second_Profile.png" alt="60second_profile" width="700" height="83" srcset="http://theorypc.ca/wp-content/uploads/2016/09/60second_Profile.png 700w, http://theorypc.ca/wp-content/uploads/2016/09/60second_Profile-300x36.png 300w" sizes="(max-width: 700px) 100vw, 700px" /> 

&nbsp;

After identifying a user with a long logon with ControlUpÂ I ran the 'Analyze Logon Duration' script:

<img class="aligncenter size-full wp-image-1695" src="http://theorypc.ca/wp-content/uploads/2016/09/51.1second_Profile.png" alt="51-1second_profile" width="547" height="356" srcset="http://theorypc.ca/wp-content/uploads/2016/09/51.1second_Profile.png 547w, http://theorypc.ca/wp-content/uploads/2016/09/51.1second_Profile-300x195.png 300w" sizes="(max-width: 547px) 100vw, 547px" /> 

&nbsp;

Jeez, 59.4 seconds to logon with 51.2 seconds of that spent on the User Profile portion. Â What is going on? Â I turned to process monitor to capture the logon process:

<img class="aligncenter size-full wp-image-1697" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.24.18-PM.png" alt="screen-shot-2016-09-07-at-8-24-18-pm" width="567" height="172" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.24.18-PM.png 567w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.24.18-PM-300x91.png 300w" sizes="(max-width: 567px) 100vw, 567px" /> 

Well, there appears to be a 1 minute gap between the cmd.exe commandÂ from when WinLogon.exe starts it. Â The stage it 'freezes' at is "Please wait for the user profile service".

&nbsp;

Since there is no data recorded by Process Monitor I tested by deleting the users profile. Â It made no difference, still 60 seconds. Â But, since I now know it's not the user profile it must be something else. Â Experience has taught me to blame the user object and look for network paths. Â 50 seconds or so just \*feels\* like a network timeout. Â So I examined the users AD object:

<img class="aligncenter size-full wp-image-1699" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM.png" alt="screen-shot-2016-09-07-at-8-46-19-pm" width="400" height="396" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM.png 400w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM-300x297.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.46.19-PM-100x100.png 100w" sizes="(max-width: 400px) 100vw, 400px" /> 

&nbsp;

Well well well, we have a path. Â Is it valid?

<img class="aligncenter size-full wp-image-1700" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.49.42-PM.png" alt="screen-shot-2016-09-07-at-8-49-42-pm" width="508" height="242" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.49.42-PM.png 508w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-07-at-8.49.42-PM-300x143.png 300w" sizes="(max-width: 508px) 100vw, 508px" /> 

&nbsp;

&nbsp;

It is not valid. Â So is my suspicion correct? Â I removed the Home Directory path and relaunched:

<img class="aligncenter size-full wp-image-1701" src="http://theorypc.ca/wp-content/uploads/2016/09/without_homedir_logon_time.png" alt="without_homedir_logon_time" width="706" height="82" srcset="http://theorypc.ca/wp-content/uploads/2016/09/without_homedir_logon_time.png 706w, http://theorypc.ca/wp-content/uploads/2016/09/without_homedir_logon_time-300x35.png 300w" sizes="(max-width: 706px) 100vw, 706px" /> 

Well that's much, much better!

So now I want ControlUp to identify this potential issue. Â Unfortunately, I don't really see any events I can key in on that says 'Attempting to map home drive'. Â But what we can do is pull that AD attribute and test to see if it's valid and let us know if it's not. Â This is the output I now generate:

<img class="aligncenter size-full wp-image-1702" src="http://theorypc.ca/wp-content/uploads/2016/09/new_script.png" alt="new_script" width="741" height="432" srcset="http://theorypc.ca/wp-content/uploads/2016/09/new_script.png 741w, http://theorypc.ca/wp-content/uploads/2016/09/new_script-300x175.png 300w" sizes="(max-width: 741px) 100vw, 741px" /> 

&nbsp;

I revised the messaging slightly as I've found theÂ 'Group Policy' phase can be affected if GPP Drive Maps reference the home directory attribute as well.

&nbsp;

So I [took my previous script](http://theorypc.ca/2016/08/31/controlup-dissecting-logon-times-a-step-further-printer-loading/)Â and updated it further. Â This time with a check for valid home directories. Â I also added some window sizing information to give greater width for the response as 'Interim Delay' was getting truncated when there were long printer names. Â Here is the further updated script:

<pre class="lang:ps decode:true ">#expand output window so it looks better in ControlUp
$pshost = get-host
$pswindow = $pshost.ui.rawui
$newsize = $pswindow.buffersize
$newsize.height = 3000
$newsize.width = 128
$pswindow.buffersize = $newsize


$newsize = $pswindow.windowsize
$newsize.height = 50
$newsize.width = 128
$pswindow.windowsize = $newsize
function Get-PsEvent {
 
 param (
 [String]$PhaseName,
 [String]$StartProvider,
 [String]$EndProvider,
 [String]$StartXPath,
 [String]$EndXPath,
 [int]$CUAddition
 )

 #$ErrorActionPreference = 'SilentlyContinue'

 try {
 $StartEvent = Get-WinEvent -MaxEvents 1 -ProviderName $StartProvider -FilterXPath $StartXPath -ErrorAction Stop
 if ($StartProvider -eq 'Microsoft-Windows-Security-Auditing' -and $EndProvider -eq 'Microsoft-Windows-Security-Auditing') {
 $EndEvent = Get-WinEvent -MaxEvents 1 -ProviderName $EndProvider -FilterXPath ("{0}{1}" -f $EndXPath,@"

and *[EventData[Data[@Name='ProcessId'] 
and (Data=`'$($StartEvent.Properties[4].Value)`')]] 
"@)

 }
 elseif ($CUAddition) {
 $EndEvent = $StartEvent
 $EndEvent = ($StartEvent.TimeCreated).AddSeconds($CUAddition)

 }

 else {
 $EndEvent = Get-WinEvent -MaxEvents 1 -ProviderName $EndProvider -FilterXPath $EndXPath
 }
 }

 catch {
 if ($PhaseName -ne 'Citrix Profile Mgmt') {
 if ($StartProvider -eq 'Microsoft-Windows-Security-Auditing' -or $EndProvider -eq 'Microsoft-Windows-Security-Auditing' ) {
 "Could not find $PhaseName events (requires audit process tracking)"
 }
 else {
 "Could not find $PhaseName events"
 }
 Return
 }
 }
 $EventInfo = @{}

 if ((($EndEvent).GetType()).Name -eq 'DateTime') {
 $Duration = New-TimeSpan -Start $StartEvent.TimeCreated -End $EndEvent
 $EventInfo.EndTime = $EndEvent
 }
 else {
 $Duration = New-TimeSpan -Start $StartEvent.TimeCreated -End $EndEvent.TimeCreated
 $EventInfo.EndTime = $EndEvent.TimeCreated 

 }

 $Script:EventProperties = $StartEvent.Properties

 $EventInfo.PhaseName = $PhaseName
 $EventInfo.StartTime = $StartEvent.TimeCreated

 $EventInfo.Duration = $Duration.TotalSeconds

 $PSObject = New-Object -TypeName PSObject -Property $EventInfo

 if ($EventInfo.Duration) {
 $Script:Output += $PSObject
 }
}

function Get-LogonDurationAnalysis {

param (
 [Parameter(Mandatory=$true)] 
 [String]$Username,
 [Parameter(Mandatory=$true)] 
 [String]$UserDomain,
 [Parameter(Mandatory=$true)] 
 [String]$clientName,
 [int]$CUDesktopLoadTime
 )

$ErrorActionPreference = 'SilentlyContinue'

$Script:Output = @()


try {

$LogonEvent = Get-WinEvent -MaxEvents 1 -ProviderName Microsoft-Windows-Security-Auditing -FilterXPath @"
*[System[(EventID='4624')]] 
and *[EventData[Data[@Name='TargetUserName'] 
and (Data=`"$UserName`")]] 
and *[EventData[Data[@Name='TargetDomainName'] 
and (Data=`"$UserDomain`")]] 
and *[EventData[Data[@Name='LogonType'] 
and (Data=`"2`" or Data=`"10`" or Data=`"11`")]]
and *[EventData[Data[@Name='ProcessName'] 
and (Data=`"C:\Windows\System32\winlogon.exe`")]]
"@ -ErrorAction Stop

}

catch {
Throw 'Could not find EventID 4624 (Successfully logged on event) in the Windows Security log.'
}

$Logon = New-Object -TypeName PSObject

Add-Member -InputObject $Logon -MemberType NoteProperty -Name LogonTime -Value $LogonEvent.TimeCreated
Add-Member -InputObject $Logon -MemberType NoteProperty -Name FormatTime -Value (Get-Date -Date $LogonEvent.TimeCreated -UFormat %r)
Add-Member -InputObject $Logon -MemberType NoteProperty -Name LogonID -Value ($LogonEvent.Properties[7]).Value
Add-Member -InputObject $Logon -MemberType NoteProperty -Name WinlogonPID -Value ($LogonEvent.Properties[16]).Value
Add-Member -InputObject $Logon -MemberType NoteProperty -Name UserSID -Value ($LogonEvent.Properties[4]).Value


$ISO8601Date = Get-Date -Date $Logon.LogonTime
$ISO8601Date = $ISO8601Date.ToUniversalTime()
$ISO8601Date = $ISO8601Date.ToString("s")





#test to see if cmd.exe was launched by Winlogon.exe instead of userinit.exe
if ($KB924034_fix = Get-WinEvent -MaxEvents 1 -ProviderName Microsoft-Windows-Security-Auditing -FilterXPath @"
*[System[(EventID='4688')]]
and *[EventData[Data[@Name='ProcessId'] 
and (Data=`'$($Logon.WinlogonPID)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data='C:\Windows\System32\cmd.exe')]] 
"@ -ErrorAction Stop) {
 #write-host "KB924034 tweak found. Setting ID of this process as the parent of userinit.exe2"
 $UserinitXPath = @"
*[System[(EventID='4688')]]
and *[EventData[Data[@Name='ProcessId'] 
and (Data=`'$($KB924034_fix.Properties[4].Value)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data='C:\Windows\System32\userinit.exe')]] 
"@
 } else {
$UserinitXPath = @"
*[System[(EventID='4688')]]
and *[EventData[Data[@Name='ProcessId'] 
and (Data=`'$($Logon.WinlogonPID)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data='C:\Windows\System32\userinit.exe')]] 
"@
}






$NPStartXpath = @"
*[System[(EventID='4688')]]
and *[EventData[Data[@Name='ProcessId'] 
and (Data=`'$($Logon.WinlogonPID)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data='C:\Windows\System32\mpnotify.exe')]] 
"@

$NPEndXPath = @"
*[System[(EventID='4689')]] 
and *[EventData[Data[@Name='ProcessName'] 
and (Data=`"C:\Windows\System32\mpnotify.exe`")]] 
"@

$ProfStartXpath = @"
*[System[(EventID='10')
and TimeCreated[@SystemTime > '$ISO8601Date']]]
and *[EventData[Data and (Data='$UserName')]]
"@

$ProfEndXpath = @"
*[System[(EventID='1')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[System[Security[@UserID='$($Logon.UserSID)']]]
"@

$UserProfStartXPath = @"
*[System[(EventID='1')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[System[Security[@UserID='$($Logon.UserSID)']]]
"@

$UserProfEndXPath = @"
*[System[(EventID='2')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[System[Security[@UserID='$($Logon.UserSID)']]]
"@

$GPStartXPath = @"
*[System[(EventID='4001')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[EventData[Data[@Name='PrincipalSamName'] 
and (Data=`"$UserDomain\$UserName`")]] 
"@

$GPEndXPath = @"
*[System[(EventID='8001')]]
and *[EventData[Data[@Name='PrincipalSamName'] 
and (Data=`"$UserDomain\$UserName`")]] 
"@

$GPScriptStartXPath = @"
*[System[(EventID='4018')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[EventData[Data[@Name='PrincipalSamName'] 
and (Data=`"$UserDomain\$UserName`")]] 
and *[EventData[Data[@Name='ScriptType'] 
and (Data='1')]]
"@

$GPScriptEndXPath = @"
*[System[(EventID='5018')
and TimeCreated[@SystemTime>='$ISO8601Date']]]
and *[EventData[Data[@Name='PrincipalSamName'] 
and (Data=`"$UserDomain\$UserName`")]] 
and *[EventData[Data[@Name='ScriptType'] 
and (Data='1')]]
"@

$ShellXPath = @"
*[System[(EventID='4688')]] 
and *[EventData[Data[@Name='SubjectLogonId'] 
and (Data=`'$($Logon.LogonID)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data=`"C:\Program Files (x86)\Citrix\system32\icast.exe`" or Data=`"C:\Windows\explorer.exe`")]]
"@

$ExplorerXPath = @"
*[System[(EventID='4688')]] 
and *[EventData[Data[@Name='SubjectLogonId'] 
and (Data=`'$($Logon.LogonID)`')]] 
and *[EventData[Data[@Name='NewProcessName'] 
and (Data=`"C:\Windows\explorer.exe`")]]
"@




Get-PsEvent -PhaseName 'Network Providers' -StartProvider 'Microsoft-Windows-Security-Auditing' -EndProvider 'Microsoft-Windows-Security-Auditing' `
-StartXPath $NPStartXpath -EndXPath $NPEndXPath

if (Get-WinEvent -ListProvider 'Citrix Profile management') {

 Get-PsEvent -PhaseName 'Citrix Profile Mgmt' -StartProvider 'Citrix Profile management' -EndProvider 'Microsoft-Windows-User Profiles Service' `
 -StartXPath $ProfStartXpath -EndXPath $ProfEndXpath
}

Get-PsEvent -PhaseName 'User Profile' -StartProvider 'Microsoft-Windows-User Profiles Service' -EndProvider 'Microsoft-Windows-User Profiles Service' `
-StartXPath $UserProfStartXPath -EndXPath $UserProfEndXPath

Get-PsEvent -PhaseName 'Group Policy' -StartProvider 'Microsoft-Windows-GroupPolicy' -EndProvider 'Microsoft-Windows-GroupPolicy' `
-StartXPath $GPStartXPath -EndXPath $GPEndXPath

Get-PsEvent -PhaseName 'GP Scripts' -StartProvider 'Microsoft-Windows-GroupPolicy' -EndProvider 'Microsoft-Windows-GroupPolicy' `
-StartXPath $GPScriptStartXPath -EndXPath $GPScriptEndXPath

if ($Script:EventProperties[3].Value -eq $true) {
 if ($Script:Output[-1].PhaseName -eq 'GP Scripts') {
 $Script:Output[-1].PhaseName = 'GP Scripts (sync)'
 }
}
else {
 if ($Script:Output[-1].PhaseName -eq 'GP Scripts') {
 $Script:Output[-1].PhaseName = 'GP Scripts (async)'
 }
}


Get-PsEvent -PhaseName 'Pre-Shell (Userinit)' -StartProvider 'Microsoft-Windows-Security-Auditing' -EndProvider 'Microsoft-Windows-Security-Auditing' `
-StartXPath $UserinitXPath -EndXPath $ShellXPath


if ($CUDesktopLoadTime) {
Get-PsEvent -PhaseName 'Shell' -StartProvider 'Microsoft-Windows-Security-Auditing' -StartXPath $ExplorerXPath -CUAddition $CUDesktopLoadTime

}

if ($Script:Output[-1].PhaseName -eq 'Shell' -or $Script:Output[-1].PhaseName -eq 'Pre-Shell (Userinit)') {

$TotalDur = "{0:N1}" -f (New-TimeSpan -Start $Logon.LogonTime -End $Script:Output[-1].EndTime | Select-Object -ExpandProperty TotalSeconds) `
+ " seconds"
}

else
{

$TotalDur = 'N/A'

}

#############NEED NEW ARGUMENT: CLIENTNAME
###################### Find how long it took to load printers into the session
###################### This impacts logon when 'wait for printers before starting application' is set
###################### This will not find any printers if the 'wait' is not set because (odds are anyways)
###################### that no printer events will be generated before pre-shell ends.
if (-not($clientName -eq "0")) {
 if (-not(($script:output | where {$_.phaseName -eq "Pre-Shell (Userinit)"}).Duration -eq $null)) {
 $preShellEndTime = (($script:output | where {$_.phaseName -eq "Pre-Shell (Userinit)"}).EndTime)
 #set end search
 $endShellTime = $Logon.LogonTime + [timespan]::FromSeconds($preShellDuration)


 $PrinterSearchISO8601Date = $preShellEndTime
 $PrinterSearchISO8601Date = $PrinterSearchISO8601Date.ToUniversalTime()
 $PrinterSearchISO8601Date = $PrinterSearchISO8601Date.ToString("s")


 $PrinterStartXPath = @"
*[System[TimeCreated[@SystemTime>='$ISO8601Date' 
and @SystemTime<='$PrinterSearchISO8601Date']]]
"@

 $printerEvents = Get-WinEvent -ProviderName "Microsoft-Windows-PrintService" -FilterXPath $PrinterStartXPath | ?{$_.message -like "*$clientName*"} | sort -Property TimeCreated

 #get list of printers:
 $listOfPrinters = @()
 foreach ($printerEvent in $printerEvents) {
 if ($printerEvent.Id -eq "300") {
 $listOfPrinters += ($printerEvent.Message -split "Printer " -split " on " -split "\(from")[1]
 }
 }

 #printer total load time
 $EventInfo = @{}
 $Duration = New-TimeSpan -Start $printerEvents[0].TimeCreated -End $printerEvents[-1].TimeCreated
 $phaseName = "Pre-Shell Printer load time:"
 $EventInfo.PhaseName = $PhaseName
 $EventInfo.Duration = $Duration.TotalSeconds
 $EventInfo.EndTime = $printerEvents[-1].TimeCreated
 $EventInfo.StartTime = $printerEvents[0].TimeCreated

 $PSObject = New-Object -TypeName PSObject -Property $EventInfo

 if ($EventInfo.Duration) {
 $Script:Output += $PSObject
 }

 #get the timespan of each printer creation start event ("Settings for printer X") and end event ("Printer X on Y")
 $EventInfo = @{}
 foreach ($printerEvent in $printerEvents) {
 foreach ($printer in $listOfPrinters) {
 $phaseName = "Printer: $printer"
 if ($printerEvent.message -like "*$printer*" -and $printerEvent.Id -eq "306") {
 $printerEventStart = $printerEvent.TimeCreated
 }
 if ($printerEvent.message -like "*$printer*" -and $printerEvent.Id -eq "300") {
 $printerEventEnd = $printerEvent.TimeCreated
 $Duration = New-TimeSpan -Start $printerEventStart -End $printerEventEnd
 $EventInfo.PhaseName = $PhaseName
 $EventInfo.Duration = $Duration.TotalSeconds
 $EventInfo.EndTime = $printerEventEnd
 $EventInfo.StartTime = $printerEventStart

 $PSObject = New-Object -TypeName PSObject -Property $EventInfo

 if ($EventInfo.Duration) {
 $Script:Output += $PSObject
 }
 }


 }
 }
 }
}
###################### Printer scan finished

###################### Query AD for home directory attribute and test to see if it works
$homedir = ([ADSISEARCHER]"samaccountname=$($args_fix[1])").Findone().Properties.homedirectory
$HomeDirMessage = "Not Present"
if ($homedir -ne $null) {
 write-host "homedir not null"
 if ((test-path $homedir) -eq $false) {
 $HomeDirMessage = """$homedir""" + " value was present but share was NOT found!`
`t`t 'User Profile' or 'Group Policy' logon phase maybe extended!"
 } else {
 $HomeDirMessage = """$homedir""" + " value was present and share was found."
 }
}



$Script:Output = $Script:Output | Sort-Object StartTime

for($i=1;$i -le $Script:Output.length-1;$i++) {

 $Deltas = New-TimeSpan -Start $Script:Output[$i-1].EndTime -End $Script:Output[$i].StartTime
 $Script:Output[$i] | Add-Member -MemberType NoteProperty -Name TimeDelta -Value $Deltas -Force



}

$Deltas = New-TimeSpan -Start $Logon.LogonTime -End $Script:Output[0].StartTime
$Script:Output[0] | Add-Member -MemberType NoteProperty -Name TimeDelta -Value $Deltas -Force

Write-Host "User name:`t $UserName`
Home directory:`t $homeDirMessage `
Logon Time:`t $($Logon.FormatTime) `
Logon Duration:`t $TotalDur"

$Format = @{Expression={$_.PhaseName};Label="Logon Phase"}, `
@{Expression={'{0:N1}' -f $_.Duration};Label="Duration (s)"}, `
@{Expression={'{0:hh:mm:ss.f}' -f $_.StartTime};Label="Start Time"}, `
@{Expression={'{0:hh:mm:ss.f}' -f $_.EndTime};Label="End Time"}, `
@{Expression={'{0:N1}' -f ($_.TimeDelta | Select-Object -ExpandProperty TotalSeconds)};Label="Interim Delay"}
$Script:Output | Format-Table $Format -AutoSize

Write-Host "Only synchronous scripts affect logon duration"


}

$args_fix = ($args[0] -split '\\')
Get-LogonDurationAnalysis -Username $args_fix[1] -UserDomain $args_fix[0] -clientName $args[1] -CUDesktopLoadTime $args[2]</pre>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->