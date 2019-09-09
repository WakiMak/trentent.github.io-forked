---
id: 520
title: Silent Install of Citrix PVS Target Device software
date: 2016-04-12T15:57:00-06:00
author: trententtye
layout: post
permalink: /2016/04/12/silent-install-of-citrix-pvs-target-device-software/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/04/silent-install-of-citrix-pvs-target.html
blogger_internal:
  - /feeds/7977773/posts/default/7657138917416941920
image: /wp-content/uploads/2016/04/Screen-2BShot-2B2016-04-07-2Bat-2B11.16.38-2BPM-1.png
categories:
  - Blog
tags:
  - Citrix
  - PowerShell
  - Provisioning Services
  - PVS
---
Installing the Citrix PVS Target Device software via a [silent](http://discussions.citrix.com/topic/277686-silent-install-pvs-target-device-software/), [non-interactive](http://nielsvdijk.blogspot.ca/2014/01/installing-citrix-provisioning-services.html) [method](http://blog.itvce.com/2012/01/03/citrix-unattended-install-of-pvs-target-device-resulting-in-vdisk-is-not-available/) fails.  Various solutions involve copying files to various locations after the install but I'm curious what it's doing when it fails.  I started to [automate one of my previous posts](http://theorypc.ca/2016/04/05/citrix-provisioning-services-updating-vmware-tools-and-target-device-software-with-all-native-tools/) and got to the stage where I needed to silently install the target device software and my install was hanging.  It was hanging because I was getting prompted to run a custom action for the MSI install.

The logging, when we are 'stuck' is at this point:

<pre class="lang:default decode:true ">MSI (s) (60:14) [23:05:16:559]: Executing op: CustomActionSchedule(Action=CVHDMP_Install.41CF0FCA_F296_4F7C_BA95_BE7EC6CD2F01,ActionType=3073,Source=BinaryData,Target=fnCVhdMp_Install,)
MSI (s) (60:14) [23:05:16:559]: Creating MSIHANDLE (787) of type 790536 for thread 4628
MSI (s) (60:64) [23:05:16:559]: Invoking remote custom action. DLL: C:\Windows\Installer\MSID755.tmp, Entrypoint: fnCVhdMp_Install
MSI (s) (60:2C) [23:05:16:559]: Generating random cookie.
MSI (s) (60:2C) [23:05:16:575]: Created Custom Action Server with PID 2980 (0xBA4).
MSI (s) (60:74) [23:05:16:606]: Running as a service.
MSI (s) (60:74) [23:05:16:606]: Hello, I'm your 64bit Elevated Non-remapped custom action server.</pre>

Logging into the server and we get the notification that a something requires an action 'Interactive Services Detection'

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://3.bp.blogspot.com/-LsVOKztSQsA/VwfgkOPUaiI/AAAAAAAABtI/AQQb2cmwYuw8T7POvxUhqa0lHk4RWuxKQ/s1600/Screen%2BShot%2B2016-04-07%2Bat%2B11.16.38%2BPM.png"><img src="https://3.bp.blogspot.com/-LsVOKztSQsA/VwfgkOPUaiI/AAAAAAAABtI/AQQb2cmwYuw8T7POvxUhqa0lHk4RWuxKQ/s320/Screen%2BShot%2B2016-04-07%2Bat%2B11.16.38%2BPM.png" width="320" height="245" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://3.bp.blogspot.com/-TEstrVCzBuQ/VwfgmBQ2IpI/AAAAAAAABtM/h3y8grQeeo4J9rCoTm1itjiw4EAtSU9Bg/s1600/Screen%2BShot%2B2016-04-07%2Bat%2B11.16.47%2BPM.png"><img src="https://3.bp.blogspot.com/-TEstrVCzBuQ/VwfgmBQ2IpI/AAAAAAAABtM/h3y8grQeeo4J9rCoTm1itjiw4EAtSU9Bg/s320/Screen%2BShot%2B2016-04-07%2Bat%2B11.16.47%2BPM.png" width="320" height="202" border="0" /></a>
</div>

If you run the install in a interactive user session the custom action appears to be this message:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://1.bp.blogspot.com/-quGGvwUwBHU/VwdLk8IymII/AAAAAAAABs0/K4taPyS2KMMUapjlvNQ3-YyBNOW0b_Hjw/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B12.10.49%2BAM.png"><img src="https://1.bp.blogspot.com/-quGGvwUwBHU/VwdLk8IymII/AAAAAAAABs0/K4taPyS2KMMUapjlvNQ3-YyBNOW0b_Hjw/s320/Screen%2BShot%2B2016-04-08%2Bat%2B12.10.49%2BAM.png" width="320" height="108" border="0" /></a>
</div>

For some reason, Citrix found it necessary to include a dialog in the installer that forces you to answer it.  When this dialog is presented in a 'silent' install it requires interactivity to continue so the install stops until it is dealt with.

So, why is this dialog appearing?  It is appearing because CFSDEP2 service was not uninstalled cleanly and the installer requires it removed for a clean install.

When uninstalling the software silently you _also_ get a 'Interactive Services Detection' dialog.  For whatever reason though, the uninstall dialog doesn't halt the process.

<div style="clear: both; text-align: center;">
</div>

What is triggering the 'interactive services detection' on uninstall and is it the cause of our CFSDEP2 not being removed?

When doing an interactive uninstall this is a screenshot of all the actions that it executes:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://1.bp.blogspot.com/-LDFbaVZ3ntI/VwfpTjN7X2I/AAAAAAAABtk/JtE2q0q46zE6SMtL_yWGnuZhdAF-SL8xQ/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.16.04%2BAM.png"><img src="https://1.bp.blogspot.com/-LDFbaVZ3ntI/VwfpTjN7X2I/AAAAAAAABtk/JtE2q0q46zE6SMtL_yWGnuZhdAF-SL8xQ/s320/Screen%2BShot%2B2016-04-08%2Bat%2B11.16.04%2BAM.png" width="320" height="101" border="0" /></a>
</div>

And you can very clearly see it deletes the CFSDEP2 file system filter driver.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://3.bp.blogspot.com/-2UrzvrKtFjo/VwfpVpLkOGI/AAAAAAAABto/gpcglCRTlp0zNDyNlsyN4T3kBhwKkv28A/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.18.22%2BAM.png"><img src="https://3.bp.blogspot.com/-2UrzvrKtFjo/VwfpVpLkOGI/AAAAAAAABto/gpcglCRTlp0zNDyNlsyN4T3kBhwKkv28A/s320/Screen%2BShot%2B2016-04-08%2Bat%2B11.18.22%2BAM.png" width="320" height="51" border="0" /></a>
</div>

What does it look like when run silently?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://3.bp.blogspot.com/-HxU-x68aNV8/Vwfq__af6jI/AAAAAAAABt0/14ktWGKrAOEeUQJAEjr0RLn2qcs9rMJUw/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.31.17%2BAM.png"><img src="https://3.bp.blogspot.com/-HxU-x68aNV8/Vwfq__af6jI/AAAAAAAABt0/14ktWGKrAOEeUQJAEjr0RLn2qcs9rMJUw/s400/Screen%2BShot%2B2016-04-08%2Bat%2B11.31.17%2BAM.png" width="400" height="80" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://2.bp.blogspot.com/-LEabhhdQ1Uc/VwfrLu7OqtI/AAAAAAAABt4/UTnlGG0I3j0MTT4I_wJ-fQhGWmcjOeTGg/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.32.08%2BAM.png"><img src="https://2.bp.blogspot.com/-LEabhhdQ1Uc/VwfrLu7OqtI/AAAAAAAABt4/UTnlGG0I3j0MTT4I_wJ-fQhGWmcjOeTGg/s400/Screen%2BShot%2B2016-04-08%2Bat%2B11.32.08%2BAM.png" width="400" height="45" border="0" /></a>
</div>

The interactive services detected prompts on uninstall (UI0Detect.exe) appears to be caused by 'runonce.exe' and the 'grpconv.exe' programs.

Is our CFSDEP2 service deleted when run silently?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://2.bp.blogspot.com/-i3GojIFQU0g/VwftelSXqlI/AAAAAAAABuI/LNJDJ5ZE4gQVjXAV_mTzVgbLlkShRD1aQ/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.41.32%2BAM.png"><img src="https://2.bp.blogspot.com/-i3GojIFQU0g/VwftelSXqlI/AAAAAAAABuI/LNJDJ5ZE4gQVjXAV_mTzVgbLlkShRD1aQ/s320/Screen%2BShot%2B2016-04-08%2Bat%2B11.41.32%2BAM.png" width="320" height="204" border="0" /></a>
</div>

It was not deleted.  Is the runonce.exe utility the cause of our CFSDEP2 service not being removed?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://3.bp.blogspot.com/-7KbRwRJUd1w/Vwfw1pxXCDI/AAAAAAAABuU/XFDjzT4p1ekTvilKsOFaTNvGTNk49TNKg/s1600/Screen%2BShot%2B2016-04-08%2Bat%2B11.55.29%2BAM.png"><img src="https://3.bp.blogspot.com/-7KbRwRJUd1w/Vwfw1pxXCDI/AAAAAAAABuU/XFDjzT4p1ekTvilKsOFaTNvGTNk49TNKg/s320/Screen%2BShot%2B2016-04-08%2Bat%2B11.55.29%2BAM.png" width="320" height="147" border="0" /></a>
</div>

It is not.  The CFSDEP2 service is removed \*before\* the runonce.exe utility it executed.  So something else is triggering it's removal.

Examining the cfsdep2.sys file in the C:Windowssystem32drivers reveals that the driver was not uninstalled with the Citrix PVS Target Device software.

It turns out that file system filter drivers can be installed and uninstalled using the command line.  
[Uninstall](https://msdn.microsoft.com/en-us/library/windows/hardware/ff557255(v=vs.85).aspx):

<pre class="lang:batch decode:true ">C:\Windows\System32\RUNDLL32.EXE SETUPAPI.DLL,InstallHinfSection DefaultUninstall 132 C:\Program Files\Citrix\Provisioning Services\drivers\cfsdep2.inf</pre>

[Install](https://msdn.microsoft.com/en-us/library/windows/hardware/ff557251(v=vs.85).aspx):

<pre class="lang:batch decode:true ">C:\Windows\System32\RUNDLL32.EXE SETUPAPI.DLL,InstallHinfSection DefaultInstall 132 C:\Program Files\Citrix\Provisioning Services\drivers\cfsdep2.inf
</pre>

So, we should just be able to add a 'Invoke-command' and execute it, right?  
I tried installing and uninstalling with both WMI and Invoke-Command:

<pre class="lang:ps decode:true ">Invoke-Command -ComputerName $VMReverseImaging -credential $PVScred -ScriptBlock {
   & 'cmd.exe' /c RUNDLL32.EXE SETUPAPI.DLL,InstallHinfSection DefaultUninstall 132 C:\Program Files\Citrix\Provisioning Services\drivers\cfsdep2.inf
   }
([WMICLASS]"\\$VMReverseImaging\ROOT\CIMV2:Win32_Process").Create("C:\Windows\system32\rundll32.exe SETUPAPI.DLL,InstallHinfSection DefaultInstall 131 C:\Program Files\Citrix\Provisioning Services\drivers\cfsdep2.inf")</pre>

Neither command worked remotely.  I could see rundll32.exe executing and exiting with status "0" which implies success.  But the commands themselves didn't \*actually\* work.  When I executed the uninstall remotely neither the CFSDEP2.SYS file was deleted nor was the service uninstalled.  Doing a procmon.exe I could see that the supplemental 'runonce.exe' and 'grpconv.exe' were not run.  The reverse was true for the install as well, the CFSDEP2.SYS was not present and the service was not installed, but the exit code was "0".  So we can't trust the exit code, we need to manually check to see if the files and service are present.

So what's happening?  It turns out, for some reason, that the file system filter driver install/uninstall \*requires\* an interactive session to complete successfully.  What is an interactive session you could use?  The SYSTEM account.  I was hoping to create a purely native script with no outside dependencies but PSEXEC will be required.  Elevating permissions in a remote powershell session is very difficult and maybe one day I'll spend some time figuring it out and documenting it, but at this point I cheaped out and decided to use psexec.exe.  To get the install/uninstall to execute remotely, you can use psexec.exe with the following lines:

<pre class="lang:ps decode:true ">$ScriptDir = Split-Path $script:MyInvocation.MyCommand.Path
& $scriptDir"\psexec.exe" \\$vmreverseimaging -i -s RUNDLL32.EXE SETUPAPI.DLL,InstallHinfSection DefaultInstall 132 C:\Program Files\Citrix\Provisioning Services\drivers\cfsdep2.inf</pre>

<div>
</div>

Or, since we are now running under the interactive SYSTEM context, we can just use the uninstall command:

<div>
  <pre class="lang:ps decode:true ">Write-Host "Uninstalling Citrix Provisioning Services Target Device x64" -ForegroundColor Yellow
$ProgramList = Get-WMIObject -Class win32_product -ComputerName $VMReverseImaging -Credential $pvsCred | ? {$_.Name -contains "Citrix Provisioning Services Target Device x64"}
$ProgID = ($ProgramList | where {$_.Name -contains "Citrix Provisioning Services Target Device x64"}).IdentifyingNumber
& $scriptDir"\psexec.exe" \\$vmreverseimaging -i -s msiexec.exe /x$ProgID /qn REBOOT=ReallySuppress</pre>
</div>

<div>
  And the install executes successfully as well under the interactive SYSTEM context:
</div>

<div>
</div>

<div>
  <pre class="lang:ps decode:true ">Write-Host "Upgrading Citrix PVS Tools..." -ForegroundColor Yellow
mkdir "\\$VMReverseImaging\c$\swinst\PVSDevice" | out-null
xcopy /i /s /e /y "\\citrixnas01.healthy.bewell.ca\ctx_images_bdc\_ISO\PVS7.7\ProvisioningServices\Device" "\\$VMReverseImaging\c$\swinst\PVSDevice"
& $scriptDir"\psexec.exe" \\$vmreverseimaging -i -s c:\swinst\PVSDevice\PVS_Device_x64.exe /s /v/qn</pre>
  
  <p>
    &nbsp;
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->