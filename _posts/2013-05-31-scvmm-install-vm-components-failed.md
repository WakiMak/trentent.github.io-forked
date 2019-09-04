---
id: 640
title: 'SCVMM: Install VM components Failed'
date: 2013-05-31T14:37:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/31/scvmm-install-vm-components-failed/
permalink: /2013/05/31/scvmm-install-vm-components-failed/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/microsoft-hyper-v-install-vm-components.html
blogger_internal:
  - /feeds/7977773/posts/default/3615242143603930281
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - procmon
---
I&#8217;m attempting to deploy a virtual machine from a template but I get this error: Install VM components: Failed.

<div style="clear: both; text-align: center;">
  <a style="font-family: 'Courier New', Courier, monospace; font-size: small; margin-left: 1em; margin-right: 1em; text-align: center;" href="http://2.bp.blogspot.com/-HKnNolwRr54/UakFb1kNkwI/AAAAAAAAATQ/JY1h3SS0QBk/s1600/1.png"><img src="http://2.bp.blogspot.com/-HKnNolwRr54/UakFb1kNkwI/AAAAAAAAATQ/JY1h3SS0QBk/s320/1.png" width="320" height="197" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-c55N2XJdwGs/UakFdYg0BOI/AAAAAAAAAUQ/sx4eEai0Mqw/s1600/2.png"><img src="http://1.bp.blogspot.com/-c55N2XJdwGs/UakFdYg0BOI/AAAAAAAAAUQ/sx4eEai0Mqw/s320/2.png" width="320" height="171" border="0" /></a>
</div>

&nbsp;

<pre class="lang:default decode:true ">Error (2940)
VMM is unable to complete the requested file transfer. The connection to the HTTP server 2012-SCVMM.bottheory.local could not be established.
Unknown error (0x80072ee2)

Recommended Action
Ensure that the HTTP service and/or the agent on the machine 2012-SCVMM.bottheory.local are installed and running and that a firewall is not blocking HTTP/HTTPS traffic on the configured port.
</pre>

<div style="clear: both; text-align: center;">
  <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">� </span>
</div>

TL;DR  
SO&#8230; � Long story short; if you are encountering this error, I would suggest booting your VHD file in a VM and re-sysprep /generalize it. � If you&#8217;ve maxed out on sysprep&#8217;s I have a post earlier in my blog on how to get around the 3-times limit and rerun sysprep. � Alternatively, you can try what I did, but I can&#8217;t gaurantee your success and replace the BCD file in your Library VHD with a BCD you \*know\* has been sysprepp&#8217;ed and try redeploying it.  
=&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;=

I&#8217;ve ensured that HTTP and HTTPS is not blocked (firewall is disabled) and the agent on my SCVMM machine is installed and running. � So this error message is somewhat useless.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-91RpU0TWAYE/UakFdjzToTI/AAAAAAAAAUc/QTLl38FAqvk/s1600/3.png"><img src="http://4.bp.blogspot.com/-91RpU0TWAYE/UakFdjzToTI/AAAAAAAAAUc/QTLl38FAqvk/s320/3.png" width="320" height="228" border="0" /></a>
</div>

So I took my two boxes, 2012-SCVMM (the SCVMM server) and S5000VXN-SERVER (the Hyper-V host) and procmon&#8217;ed them while it was attempting to &#8220;Install VM Components&#8221; to try and understand what it&#8217;s trying to do.

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-CSCUaOEX1Hg/UakFeaYB_XI/AAAAAAAAAU4/_70hB1bOU-k/s1600/4.png"><img src="http://4.bp.blogspot.com/-CSCUaOEX1Hg/UakFeaYB_XI/AAAAAAAAAU4/_70hB1bOU-k/s320/4.png" width="320" height="146" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

After reinitating the task, we can see that the vmmAgent.exe on the Hyper-V host accesses the file about 2 seconds after I submit the command to retry the job.

A few seconds after this, it appears to &#8220;CreateFile&#8221; in the WindowsTemp directory; but this is not actually correct.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://3.bp.blogspot.com/-UnOO4sy9BTY/UakFeegh-hI/AAAAAAAAAU0/PAkWqFj-eXI/s1600/5.png"><img src="http://3.bp.blogspot.com/-UnOO4sy9BTY/UakFeegh-hI/AAAAAAAAAU0/PAkWqFj-eXI/s320/5.png" width="311" height="320" border="0" /></a>
</div>

What it is really doing is \*mounting\* the VHD into a folder in the WindowsTemp directory. � You can actually watch this in action if you view Disk Management on the Hyper-V host while you execute the task.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-SZ-Q51Dnjbg/UakFeQ0dhRI/AAAAAAAAAUw/43OCE5hxzRg/s1600/6.png"><img src="http://3.bp.blogspot.com/-SZ-Q51Dnjbg/UakFeQ0dhRI/AAAAAAAAAUw/43OCE5hxzRg/s320/6.png" width="320" height="261" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-cR_M4FntCL8/UakFerOC4HI/AAAAAAAAAU8/YQFyJAR2qzw/s1600/7.png"><img src="http://4.bp.blogspot.com/-cR_M4FntCL8/UakFerOC4HI/AAAAAAAAAU8/YQFyJAR2qzw/s320/7.png" width="320" height="179" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

So now that we know it&#8217;s mounting the file as a volume this helps us narrow down on what Hyper-V is attempting to do&#8230; � And I suspect what it is attempting to do is &#8220;offline servicing&#8221; of the attached vdisk/vhd.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Wt-91NctUiU/UakFe7RrRPI/AAAAAAAAAVA/m8sjBPC-v4A/s1600/8.png"><img src="http://1.bp.blogspot.com/-Wt-91NctUiU/UakFe7RrRPI/AAAAAAAAAVA/m8sjBPC-v4A/s400/8.png" width="400" height="83" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

After attaching the vdisk the next thing it does it query the BCD file on the system. � Maybe it needs to be in a certain mode to operate? � I&#8217;m not sure&#8230;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-tV3MwDZla_Q/UakFfN2yevI/AAAAAAAAAVI/I7fqcUoxHA0/s1600/9.png"><img src="http://2.bp.blogspot.com/-tV3MwDZla_Q/UakFfN2yevI/AAAAAAAAAVI/I7fqcUoxHA0/s320/9.png" width="320" height="123" border="0" /></a>
</div>

Continuing on we can see that events are written to the Microsoft-Windows-FileShareShadowCopyProvider Operational.evtx, System.evtx, and Microsoft-Windows-Bits-CompactServer Operational.evtx event logs. � Examining each log at the time stamp showed the FileShareShadowCopyProvider and System log were just noticed of volume shadow copy starting, but the BITS event log was interesting.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-pO_gsbRxKQw/UakFcAdTDeI/AAAAAAAAATU/I54xm9N5L4s/s1600/10.png"><img src="http://2.bp.blogspot.com/-pO_gsbRxKQw/UakFcAdTDeI/AAAAAAAAATU/I54xm9N5L4s/s400/10.png" width="400" height="291" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-sI6-5lew4NA/UakFcEBouxI/AAAAAAAAATY/ZGyHYgteFjk/s1600/11.png"><img src="http://3.bp.blogspot.com/-sI6-5lew4NA/UakFcEBouxI/AAAAAAAAATY/ZGyHYgteFjk/s400/11.png" width="400" height="276" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-HU-lFdkamOU/UakFcaaTZ8I/AAAAAAAAATk/GaX6xju3NPI/s1600/12.png"><img src="http://2.bp.blogspot.com/-HU-lFdkamOU/UakFcaaTZ8I/AAAAAAAAATk/GaX6xju3NPI/s400/12.png" width="400" height="278" border="0" /></a>
</div>

It showed that it was doing something with the BCD file. � The Hyper-V host was \*serving\* it out. � I suspect it was serving it to the SCVMM.  
Looking back at the SCVMM server we see that was executing some WinRM commands. � Sadly, we do not know what commands it was trying to send.

If I mount the vhd file and check out the BCD file I can see that it appears to be corrupted in that it doesn&#8217;t know what the proper boot device should be.

<pre class="lang:batch decode:true ">C:\Windows\system32&gt;bcdedit /store "K:\boot\bcd" /enum all</pre>

&nbsp;

<pre class="lang:default decode:true ">Windows Boot Manager
--------------------
identifier              {bootmgr}
device                  unknown
description             Windows Boot Manager
locale                  en-US
inherit                 {globalsettings}
bootshutdowndisabled    Yes
default                 {default}
resumeobject            {b520e13c-48da-11e2-9a8b-00155d011700}
displayorder            {default}
                        {7619dcc9-fafe-11d9-b411-000476eba25f}
toolsdisplayorder       {memdiag}
timeout                 3

Windows Boot Loader
-------------------
identifier              {7619dcc9-fafe-11d9-b411-000476eba25f}
device                  ramdisk=[boot]\sources\boot.wim,{7619dcc8-fafe-11d9-b411
-000476eba25f}
path                    \windows\system32\boot\winload.exe
description             Windows Setup
locale                  en-US
inherit                 {bootloadersettings}
osdevice                ramdisk=[boot]\sources\boot.wim,{7619dcc8-fafe-11d9-b411
-000476eba25f}
systemroot              \windows
detecthal               Yes
winpe                   Yes
ems                     Yes

Windows Boot Loader
-------------------
identifier              {default}
device                  unknown
path                    \Windows\system32\winload.exe
description             Windows Server 2012
locale                  en-US
inherit                 {bootloadersettings}
allowedinmemorysettings 0x15000075
osdevice                unknown
systemroot              \Windows
resumeobject            {b520e13c-48da-11e2-9a8b-00155d011700}
nx                      OptOut
detecthal               Yes

Resume from Hibernate
---------------------
identifier              {b520e13c-48da-11e2-9a8b-00155d011700}
device                  unknown
path                    \Windows\system32\winresume.exe
description             Windows Resume Application
locale                  en-US
inherit                 {resumeloadersettings}
allowedinmemorysettings 0x15000075
filepath                \hiberfil.sys

Windows Memory Tester
---------------------
identifier              {memdiag}
device                  unknown
path                    \boot\memtest.exe
description             Windows Memory Diagnostic
locale                  en-US
inherit                 {globalsettings}
badmemoryaccess         Yes

EMS Settings
------------
identifier              {emssettings}
bootems                 Yes

Debugger Settings
-----------------
identifier              {dbgsettings}
debugtype               Serial
debugport               1
baudrate                115200

RAM Defects
-----------
identifier              {badmemory}

Global Settings
---------------
identifier              {globalsettings}
inherit                 {dbgsettings}
                        {emssettings}
                        {badmemory}

Boot Loader Settings
--------------------
identifier              {bootloadersettings}
inherit                 {globalsettings}
                        {hypervisorsettings}

Hypervisor Settings
-------------------
identifier              {hypervisorsettings}
hypervisordebugtype     Serial
hypervisordebugport     1
hypervisorbaudrate      115200

Resume Loader Settings
----------------------
identifier              {resumeloadersettings}
inherit                 {globalsettings}

Device options
--------------
identifier              {7619dcc8-fafe-11d9-b411-000476eba25f}
ramdisksdidevice        boot
ramdisksdipath          \boot\boot.sdi</pre>

It&#8217;s not actually corrupted though; the reason why the devices are unknown is because bcdedit isn&#8217;t finding the disk signature of the volume I mounted. � But it has the disk signature because I can boot with it without issue.

Continuing on&#8230;

In order to try and find out what commands WSMAN was sending I enabled debugview on the SCVMM server:  
<http://support.microsoft.com/kb/970066?wa=wsignin1.0>

I then reproduced the error by rerunning the Create Virtual Machine job.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-fsU-9jEWFHo/UakFcnZ0EwI/AAAAAAAAAT0/TWbDpWyJkLI/s1600/14.png"><img src="http://3.bp.blogspot.com/-fsU-9jEWFHo/UakFcnZ0EwI/AAAAAAAAAT0/TWbDpWyJkLI/s320/14.png" width="320" height="192" border="0" /></a>
</div>

DebugView gave me more information to narrow down what was happening. � It appears that the process is failing with:

<pre class="lang:default decode:true ">[4276] 10B4.000C::05/31-12:52:00.894#16BcdUtil.cs(1987): bootDevice UnknownDevice
[4276] 10B4.000C::05/31-12:52:01.334#16MountedVhd.cs(91): MountedVhd windows volume not found
[4276] 10B4.000C::05/31-12:52:01.342#16VMAdditions.cs(874): VMAdditions install failed at OS detection phase for vm AirVid
[4276] 10B4.000C::05/31-12:52:01.388#16VMAdditions.cs(874): Microsoft.VirtualManager.Engine.VmOperations.MountedSystem+BootOrSystemVolumeNotFoundException: Virtual Machine Manager cannot locate the boot or system volume on virtual machine NO_PARAM. The resulting virtual machine might not start or operate properly.</pre>

<span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /> </span>Doing some googling on this took me to a Korean Microsoft page where the following was stated:  
<http://social.technet.microsoft.com/Forums/ko-KR/momsmsmofko/thread/fedbc514-1fc0-4b55-979b-7d07babb074b/>

<pre class="lang:default decode:true ">When creating a new VM from template, and that template contains a blank VHD or a non-Windows OS or a Windows OS that has not been generalized (i.e. the OS is not sysprepped), then the job will fail because VMM expects a VM from template to go through the sysprep customization process (hence why we ask for an OS profile). VMM will crack open the VHD and check of the OS is in a sysprep state. If not, then the job will fail. To create a proper template, you can use an existing Vm with a running Windows OS (right click on it and select New Template… this will kick off sysprep in the OS and then store the VM to Library as template… this removes the original VM). Or… if you already have VHDs that have been sysprepped, simply import them to the Library and then when you create a new template… attach that VHD instead of the blank one. Last… you can create a new template that does not require a sysprepped OS by selecting ‘Customization Not Required’ from the Guest OS profile dropdown:</pre>

<http://blogs.technet.com/b/hectorl/archive/2008/08/21/digging-deeper-into-error-13206-virtual-machine-manager-cannot-locate-the-boot-or-system-volume-on-virtual-machine.aspx>

Thinking about it, I do not think my VHD was SysPrep&#8217;ed. � I find it interesting that sysprep appears to do something to the BCD file. � To find out what sysprep does to the BCD file I booted up my 2012 VHD into Windows and ran sysprep, generalize and shutdown.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-j93lsMv6bJg/UakFc18B3cI/AAAAAAAAATw/KslHskWaEd8/s1600/15.png"><img src="http://4.bp.blogspot.com/-j93lsMv6bJg/UakFc18B3cI/AAAAAAAAATw/KslHskWaEd8/s320/15.png" width="320" height="243" border="0" /></a>
</div>

<pre class="">With the VM now generalized I can crack open the VHD and see what's added to the BCD to make it so special...

C:\Users\amttye\Desktop&gt;fc before-sysprep.txt after-sysprep.txt
Comparing files before-sysprep.txt and AFTER-SYSPREP.TXT
***** before-sysprep.txt
identifier              {bootmgr}
device                  partition=K:
description             Windows Boot Manager
***** AFTER-SYSPREP.TXT
identifier              {bootmgr}
<b><u><i>device                  locate=unknown</i></u></b>
description             Windows Boot Manager
*****

***** before-sysprep.txt
toolsdisplayorder       {memdiag}
timeout                 3

***** AFTER-SYSPREP.TXT
toolsdisplayorder       {memdiag}
<b><u><i>timeout                 30</i></u></b>

*****

***** before-sysprep.txt
identifier              {default}
device                  partition=K:
path                    \Windows\system32\winload.exe
***** AFTER-SYSPREP.TXT
identifier              {default}
<b><u><i>device                  locate=\Windows\system32\winload.exe</i></u></b>
path                    \Windows\system32\winload.exe
*****

***** before-sysprep.txt
inherit                 {bootloadersettings}
allowedinmemorysettings 0x15000075
osdevice                partition=K:
systemroot              \Windows
***** AFTER-SYSPREP.TXT
inherit                 {bootloadersettings}
<b><u><i>recoveryenabled         No</i></u></b>
allowedinmemorysettings 0x15000075
<b><u><i>osdevice                locate=\Windows</i></u></b>
systemroot              \Windows
*****

***** before-sysprep.txt
nx                      OptOut
detecthal               Yes

***** AFTER-SYSPREP.TXT
nx                      OptOut

*****

***** before-sysprep.txt
identifier              {b520e13c-48da-11e2-9a8b-00155d011700}
device                  partition=K:
path                    \Windows\system32\winresume.exe
***** AFTER-SYSPREP.TXT
identifier              {b520e13c-48da-11e2-9a8b-00155d011700}
<b><u><i>device                  locate=\Windows\system32\winresume.exe</i></u></b>
path                    \Windows\system32\winresume.exe
*****

***** before-sysprep.txt
inherit                 {resumeloadersettings}
allowedinmemorysettings 0x15000075
filepath                \hiberfil.sys

***** AFTER-SYSPREP.TXT
inherit                 {resumeloadersettings}
<b><u><i>recoveryenabled         No</i></u></b>
allowedinmemorysettings 0x15000075
filedevice              locate=\hiberfil.sys
filepath                \hiberfil.sys
<b><u><i>debugoptionenabled      No</i></u></b>

*****

***** before-sysprep.txt
identifier              {memdiag}
device                  unknown
path                    \boot\memtest.exe
***** AFTER-SYSPREP.TXT
identifier              {memdiag}
<b><u><i>device                  locate=\boot\memtest.exe</i></u></b>
path                    \boot\memtest.exe
*****

Interestinginly the differences appear to be mostly "locate=%%".  I guess this would make sense as the assumption is the BCD is being moved to a new disk with a new disk signature and so it can't lock on to the existing signature.  Some other oddities is the removal of "detecthal" and explicit declarations of debugoptionenabled and recoveryenabled.  I suspect that a generalized BCD file is portable, so I'm going to extract it from this image and inject it into my previously "failing" image.  I then edited the template and removed the old VHD and added the new one.

</pre>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-gJXfmbfLAIo/UakFczvKz0I/AAAAAAAAAT4/B9UHmfrKdak/s1600/16.png"><img src="http://2.bp.blogspot.com/-gJXfmbfLAIo/UakFczvKz0I/AAAAAAAAAT4/B9UHmfrKdak/s1600/16.png" border="0" /></a>
</div>

And&#8230;&#8230;&#8230;.? � Lets go to DebugView:

<pre class="lang:default decode:true ">[4276] 10B4.000C::05/31-13:57:40.193#16SystemInformation.cs(192): SYSTEM: :\ Version=0.0 HALType=  Memory=2MB, Procs=1 Is64s=False . OSLanguage=0
[4276] 10B4.000C::05/31-13:57:40.193#16SystemInformation.cs(970): Lookup for '\??\Volume{b42a3001-c871-11e2-93f4-0015172fc019}' in MountedDevices
[4276] 10B4.000C::05/31-13:57:40.193#16SystemInformation.cs(764): DISK: 10, signature=a9083c0a #partitions 1
[4276] 10B4.000C::05/31-13:57:40.194#16SystemInformation.cs(768):  PARTITION: 10.0 Bootable=True, #LDs 1
[4276] 10B4.000C::05/31-13:57:40.194#16SystemInformation.cs(775):   LOGICALDRIVE: \\?\Volume{b42a3001-c871-11e2-93f4-0015172fc019}\(\\?\Volume{b42a3001-c871-11e2-93f4-0015172fc019}\), WindowsDrive=True, IsBoot=False, BootSectorType=Bootmgr, FullSize=136363114496, [1048576-136364163072]
[4276] 10B4.000C::05/31-13:57:40.194#16MountedVhd.cs(623): FindBootVol \\?\Volume{b42a3001-c871-11e2-93f4-0015172fc019}\, found boot drive candidate
[4276] 10B4.000C::05/31-13:57:40.194#16MountedVhd.cs(308): Bootmgr boot loader
[4276] 10B4.000C::05/31-13:57:40.194#16MountedVhd.cs(1053): Trying to get the mounted point for volume \\?\Volume{b42a3001-c871-11e2-93f4-0015172fc019}\.
[4276] 10B4.000C::05/31-13:57:40.196#16CommonUtils.cs(171): Fixup & Copy 'C:\Windows\TEMP\tmp2E1.tmp\boot\bcd' to C:\Users\svc_scvmm\AppData\Local\Temp\tmpA2F4.tmp
[4276] 10B4.000C::05/31-13:57:40.196#04BitsDeployer.cs(1392): Deploy file C:\Windows\TEMP\tmp2E1.tmp\boot\bcd from s5000vxn-server.bottheory.local to C:\Users\svc_scvmm\AppData\Local\Temp\tmpA2F4.tmp on 2012-SCVMM.bottheory.local</pre>

<span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /> </span>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-4nwcl24wIgo/UakFdEJV8BI/AAAAAAAAAUA/9EU7ZTqAkKc/s1600/17.png"><img src="http://4.bp.blogspot.com/-4nwcl24wIgo/UakFdEJV8BI/AAAAAAAAAUA/9EU7ZTqAkKc/s400/17.png" width="400" height="216" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-HNjlijycQho/UakFdeI4SzI/AAAAAAAAAUU/OcO42VkRgNM/s1600/18.png"><img src="http://3.bp.blogspot.com/-HNjlijycQho/UakFdeI4SzI/AAAAAAAAAUU/OcO42VkRgNM/s400/18.png" width="400" height="168" border="0" /></a>
</div>

<span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /> </span>  
Voila! � It appears much better than before. � It found the drive correctly and checked the BCD file and found it is the &#8220;Generalize&#8221; state. � The \*actual\* image I originally made was NOT sysprep&#8217;ed and all I did was replace the BCD file, but it allowed it to continue beyond and the machine actually completed the imaging process properly. � It joined the domain and whatever else the answer file was I gave it in SCVMM.

It appears I was correct in my earlier assumption about what SCVMM is doing. � It goes and grabs the BCD file and transfers it from the target machine to itself, &#8220;fixes it up&#8221; (not sure what it&#8217;s doing at this stage precisely), then sends it back for injection. � Certainly a bit of a complicated process with a fair bit that can go wrong, but shame on Microsoft for having such a poor error message. � I suspect it wouldn&#8217;t have required much effort to push out a real message; something to the effect of, &#8220;The BCD of this vDisk does not appear to have been through the sysprep /generalize process. � Please rerun sysprep against the image and try again&#8221;.

SO&#8230; � Long story short; if you are encountering this error, I would suggest booting your VHD file in a VM and re-sysprep /generalize it. � If you&#8217;ve maxed out on sysprep&#8217;s I have a post earlier in my blog on how to get around the 3-times limit and rerun sysprep. � Alternatively, you can try what I did, but I can&#8217;t gaurantee your success and replace the BCD file in your Library VHD with a BCD you \*know\* has been sysprepp&#8217;ed and try redeploying it.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->