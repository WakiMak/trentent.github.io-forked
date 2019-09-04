---
id: 521
title: ConvertFrom-MCLI for PVS 7.6 POSH module
date: 2016-04-06T14:20:00-06:00
author: trententtye
layout: post
permalink: /2016/04/06/convertfrom-mcli-for-pvs-7-6-posh-module/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/04/convertfrom-mcli-for-pvs-76-posh-module.html
blogger_internal:
  - /feeds/7977773/posts/default/5951779682739462282
categories:
  - Blog
tags:
  - Citrix
  - PowerShell
  - Provisioning Services
---
[Martin Zugec made a POSH script](https://www.citrix.com/blogs/2012/07/23/provisioning-services-and-powershell-easier-method/) to convert output from mcli.exe to a PowerShell object, but his script fails with the new PVS 7.6 POSH module. Â From looking at his script he is trying to key on 'spaces' to set the properties of the objects. Â MCLI.exe outputs the properties with spaces. Â PVS 7.6 POSH module fails because it doesn't format it's output as spaces.

MCLI.EXE output:

<pre class="lang:default decode:true ">Executing: Get DISKVERSION
Get succeeded. 2 record(s) returned.
Record #1
    access: 0
 
Record #2
    access: 1</pre>

POSH module output:

<pre class="lang:default decode:true ">Executing: Get diskversion
Get succeeded. 2 record(s) returned.
Record #1
access: 0
 
Record #2
access: 1</pre>

Notice the subtle difference? No spaces.

To get Martin's original script working we need to change the script to look for another character we can key on. Fortunately(?) it \*appears\* that the PVS POSH module outputs properties to start with a lower case letter. If we modify his script here:

<pre class="lang:ps decode:true ">If ($Line[0] -eq " " -or $Line.StartsWith("Record #")) {</pre>

To this:

<pre class="lang:ps decode:true ">If ($Line[0] -cmatch  "[a-z]" -or $Line.StartsWith("Record #")) {</pre>

It now outputs properties correctly from the PVS POSH module:

<pre class="lang:ps decode:true ">PS C:\swinst\VMTools_and_TargetDeviceSoftwareUpdate> mcli-get diskversion -p diskLocatorName=&65Tn02,siteName=BDC,storeName=& -f access | ConvertFrom-MCLI
Retrieved 2 objects
 
access                                                                                                                     
------                                                                                                                     
0                                                                                                                          
1</pre>

The full modified script is here:

<pre class="lang:ps decode:true ">Function ConvertFrom-MCLI {
 
 Begin {
  [array]$PvsLines = @()
 }
  
 Process {
  $PvsLines += $_
 }
  
 End {
  [array]$ResultArray = @()
  :ParseLines ForEach ($Line in $PvsLines) {
   If ($Line.Length -eq 0) {
                Continue
                }
   If ($Line[0] -cmatch  "[a-z]" -or $Line.StartsWith("Record #")) {
    # New object reference
    If ($Line.StartsWith("Record #")) {
     [Object]$Script:PvsObject = New-Object PSObject
     $ResultArray += $Script:PvsObject
                    Continue ParseLines
    } ElseIf ($Script:PvsObject -is [Object]) {
     $Script:PvsObject | Add-Member -MemberType NoteProperty -Name  $([System.Text.RegularExpressions.Regex]::Replace($Line.Substring(0, $Line.IndexOf(":")),"[^1-9a-zA-Z_]","")) -Value $($Line.Substring($Line.IndexOf(":") + 2))
    }
   }
  }
  Write-Host "Retrieved $($ResultArray.Count) objects"
  Return $ResultArray
 }
}</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->