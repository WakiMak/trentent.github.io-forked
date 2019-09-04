---
id: 525
title: 'AppV5 - Package fails to load with error 0x79100E100-0xC (Starring Procmon)'
date: 2016-03-17T13:09:00-06:00
author: trententtye
layout: post
permalink: /2016/03/17/appv5-package-fails-to-load-with-error-0x79100e100-0xc-starring-procmon/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/03/appv5-package-fails-to-load-with-error_17.html
blogger_internal:
  - /feeds/7977773/posts/default/3650713511399974837
image: /wp-content/uploads/2016/03/1-5.png
categories:
  - Blog
tags:
  - AppV
  - procmon
  - scripting
---
We were having an issue with a AppV5 package loading and we received the following error message:  
<span style="font-family: 'courier new' , 'courier' , monospace;">Event ID 1008</span>  
<span style="font-family: 'courier new' , 'courier' , monospace;">Package {b8b01729-ed31-4d77-a859-dbd8b82a3372} version {e1d21ac7-84f0-4ab7-998f-e3258be91298} failed configuration in folder 'D:AppVDataPackageInstallationRootB8B01729-ED31-4D77-A859-DBD8B82A3372E1D21AC7-84F0-4AB7-998F-E3258BE91298' with error 0x79100E10-0xC.</span>

This message is preceeded by this message:  
<span style="font-family: 'courier new' , 'courier' , monospace;">Event ID 4009</span>  
<span style="font-family: 'courier new' , 'courier' , monospace;">machine script for event AddPackage with command line: 'cmd.exe' exited with failure error code: Incorrect function.. Because Rollback is set to true in the script definition, the current AppV Client operation was rolled back.</span>

I started investigating. Â The first thing I did was open a powershell window and tried to load the package via the command line:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-5.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-5-300x64.png" width="320" height="68" border="0" /></a>
</div>

I then looked at our DynamicDeployment XML file and examined the script it is trying to launch:

<pre class="lang:default decode:true "><machinescripts>
  <addpackage>
    <path>cmd.exe</Path>
    <arguments>/c "C:\PublishedApplications\AHS-STIII_ATOP_MKLINK_INSTALL.cmd"</Arguments>
    <wait RollbackOnError="true" Timeout="30"/>
  </AddPackage>
  <removepackage>
    <path>cmd.exe</Path>
    <arguments>/c "C:\PublishedApplications\AHS-STIII_ATOP_MKLINK_REMOVE.cmd"</Arguments>
    <wait RollbackOnError="false" Timeout="60"/>
  </RemovePackage>
</MachineScripts></pre>

The script it's trying to launch looks like this:

<pre class="lang:batch decode:true ">@ECHO OFF
 
cmd.exe /c "C:\PublishedApplications\AHS-ATOP.cmd" INSTALL & "C:\PublishedApplications\AHS-SoftWorksGroup-ScreenTestIII.cmd" INSTALL</pre>

Since this is a Machine Script, I started a **process monitor capture**, <u>executed my command</u>, **stopped the capture** then used the "Process Tree"

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/2-2.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/2-2-300x76.png" width="320" height="81" border="0" /></a>
</div>

and clicked on AppVClient.exe and clicked "Include Subtree".

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/3-3.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/3-3-300x67.png" width="320" height="71" border="0" /></a>
</div>

and used the "show process and thread activity". Â I filtered on 'Detail' 'Begins with' and set it for both Parent and Exit so I can look at the process path and exit codes:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/5-3.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/5-3-300x126.png" width="320" height="134" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/6-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/6-1-300x40.png" width="320" height="42" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

The 'exit code' is '1' (Exit Status: 1) which means an error occurred that caused the script to fail. Â So now we dive into that script and see why it's failing:

<pre class="lang:batch decode:true ">@ECHO OFF
CLS
 
SET ATOPPATH="C:\Program Files (x86)\Alberta Health Services\ATOP"
IF /I [%1] EQU [PREPROD] (
  SET ATOPPATH="C:\Program Files (x86)\Alberta Health Services\ATOP Preprod"
)
 
IF /I [%1] EQU [INSTALL] (
  mklink /D "C:\Program Files (x86)\Alberta Health Services" \\fileserver\atop_share\ATOP
  EXIT
)
 
IF /I [%1] EQU [REMOVE] (
  rmdir /s /q "C:\Program Files (x86)\Alberta Health Services"
  EXIT
)</pre>

From looking at the script, it's trying to create a new 'mklink' path. Â If I try and run this command manually, I get the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/7-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/7-1-300x67.png" width="320" height="71" border="0" /></a>
</div>

So this is where the errorlevel (also exit code) is being set. Â The last error level is 1 which becomes the exit code once the script runs 'EXIT.

So, there are two methods I can think of to solve this problem.

1) I can set EXIT /B 0 to always set EXIT to report and error code of '0'.  
2) Check to see if the path already exists and then EXIT

I chose to modify the script to exit if the path exists. Â I changed it like so:

<pre class="lang:batch decode:true ">IF /I [%1] EQU [INSTALL] (
  IF EXIST "C:\Program Files (x86)\Alberta Health Services" EXIT
  mklink /D "C:\Program Files (x86)\Alberta Health Services" \\fileserver\atop_share\ATOP
  EXIT
)</pre>

I cleared procmon, set it to trace again and attempted to run the add-appvclientpackage command again.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/8-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/8-1-300x75.png" width="320" height="80" border="0" /></a>
</div>

I selected 'AppVClient.exe' and clicked 'Include Subtree'

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/9-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/9-1-300x48.png" width="320" height="51" border="0" /></a>
</div>

This time we can see that the 'AHS-ATOP.cmd' script has an 'Exit Status: 0' which means it completed successfully. Â But, the next script, 'AHS-SoftWorksGroup-ScreenTestIII.cmd' with the parameter 'INSTALL' fails with 'Exit Status: 1'. Â Again, we look into the script...

<pre class="lang:batch decode:true ">IF /I [%1] EQU [INSTALL] (
  mklink /D "C:\Program Files\Softworks Group Inc" \\fileserver\share$\Screening_Services
  EXIT
)</pre>

We can see it has the same flaw. Â I then modified the script to add the same IF EXISTS check. Â I then cleared procmon and reattempted to add the package.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/03/10-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/10-1-300x146.png" width="320" height="155" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Success!!!
    </td>
  </tr>
</table>

Hurray! Â The package loaded and the scripts ran correctly.

An alternative to all of this is I could have changed the 'rollback' to 'false' in the DeploymentConfig.xml file, but I would rather the package \*not\* load if the 'INSTALL' script fails for whatever reason. Â This ensures an error is generated that needs to be dealt with rather than potentially having a half-working package. Â These INSTALL scripts were simple enough that a simple directory check would suffice to ensure it's working and that's what I've done.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->