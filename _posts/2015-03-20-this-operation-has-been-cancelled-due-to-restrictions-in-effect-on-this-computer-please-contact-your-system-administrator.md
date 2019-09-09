---
id: 574
title: This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator.
date: 2015-03-20T13:38:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/this-operation-has-been-cancelled-due-to-restrictions-in-effect-on-this-computer-please-contact-your-system-administrator/
permalink: /2015/03/20/this-operation-has-been-cancelled-due-to-restrictions-in-effect-on-this-computer-please-contact-your-system-administrator/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/this-operation-has-been-cancelled-due.html
blogger_internal:
  - /feeds/7977773/posts/default/429722697645956679
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
---
[Window Title]  
Restrictions

[Content]  
This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator.

[OK]

We encountered this dialog with Citrix & 6.5 sessions when using various programs.  The issue is this dialog is generated when a button would open a dialog and the default path was %userprofile% or %temp% or any other path that defaulted and started with C:.  The cause of it is a [group policy setting](https://support.microsoft.com/en-us/kb/231289).  If you disable the viewing of the drive letter that the dialog is trying to default to; this message appears.  For us, some programs were trying to default to %TEMP% which is trying to go "C:\Users\myTest\AppData\Local\Temp\1".  Since "C:" is in the path, this dialog was disallowed and the message popped up.

NOTE: This message will only pop up if the program uses the standard Windows dialog.  If a program uses a custom dialog it will not receive this message.

To avoid this issue we wanted to default to the users 'My Documents' folder, which is redirected and stored on an allowed drive letter.  But My Documents isn't a variable we can utilize.  But we can create a script that launches prior to our program launching and create the variable:

<pre class="lang:batch decode:true ">::Gets the personal folder location and sets it as a variable
  > %TEMP%\personal.ps1 ECHO $var = Get-ItemProperty -Path 'Registry::HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders' -Name Personal
 >> %TEMP%\personal.ps1 ECHO $var.personal
 
for /f "tokens=*" %%A IN ('powershell.exe -executionpolicy bypass -file "%TEMP%\personal.ps1"') DO set MYDOCS=%%A
::Gets the personal folder location and sets it as a variable</pre>

<div>
  I used PowerShell.exe to get and return the variable as Reg.exe is disallowed and reg.exe wasn't returning the full path if the 'Personal' key had a space in it; powershell would return the full value.
</div>

<div>
</div>

<div>
  In addition to setting this variable, our program(s) could now be configured to default to the new variable.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->