---
id: 2012
title: Optimizing PVS Target Device Boot for Windows 10 (part 1)
date: 2017-02-03T15:32:54-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/02/03/1828-revision-v1/
permalink: /2017/02/03/1828-revision-v1/
---
&#8216;ve been exploring improving our [target](https://theorypc.ca/2016/12/30/lets-make-pvs-target-device-booting-great-again-part-1/) [device](https://theorypc.ca/2017/01/05/lets-make-pvs-target-device-booting-great-again-part-2/) [boot](https://theorypc.ca/2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-windows-performance-toolkit/) [time](https://theorypc.ca/2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-process-monitor/) with Citrix PVS. Â In this post I&#8217;m going to look at optimizing PVS Target Device Boot for Windows 10.

The first thing that is required is to get a baseline. Â Since this is PVS I rebooted the target device several times to cache it on the PVS server. Â After doing so, I was able to establish a boot time of around 14 seconds.

<img class="aligncenter size-full wp-image-1829" src="http://theorypc.ca/wp-content/uploads/2016/12/1.png" alt="" width="451" height="483" srcset="http://theorypc.ca/wp-content/uploads/2016/12/1.png 451w, http://theorypc.ca/wp-content/uploads/2016/12/1-280x300.png 280w" sizes="(max-width: 451px) 100vw, 451px" /> 

Step 2 is understand what is happening in those 14 seconds. Â What I did [was embed xperf into the PVS image](https://theorypc.ca/2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-windows-performance-toolkit/). Â Every reboot then recorded an xperf boot that I could take a look at:

<img class="aligncenter size-full wp-image-1830" src="http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Baseline.png" alt="" width="746" height="340" srcset="http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Baseline.png 746w, http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Baseline-300x137.png 300w" sizes="(max-width: 746px) 100vw, 746px" /> 

&nbsp;

Where does PVS get its &#8220;14 seconds&#8221; from? Â It gets the time from when the system booted to when the BNDevice.exe service starts:

<img class="aligncenter size-full wp-image-1831" src="http://theorypc.ca/wp-content/uploads/2016/12/BNDevice.png" alt="" width="729" height="627" srcset="http://theorypc.ca/wp-content/uploads/2016/12/BNDevice.png 729w, http://theorypc.ca/wp-content/uploads/2016/12/BNDevice-300x258.png 300w" sizes="(max-width: 729px) 100vw, 729px" /> 

The largest portion of the 14 second boot time is the &#8220;Pre Session Init&#8221;. Â Hopefully, we can find something in there to reduce our logon times as the 6.16 seconds account for 44% of the boot time. Â How can we look into the &#8220;Pre Session Init&#8221; portion of the boot sequence? Â Fortunately, xperf has various views that will show information of that&#8217;s happening during that time. Â The first I found is &#8216;Transient Lifetime By Process, Image&#8217;. Â Here&#8217;s what it showed:

<img class="aligncenter size-full wp-image-1832" src="http://theorypc.ca/wp-content/uploads/2016/12/Big_Gap.png" alt="" width="746" height="361" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Big_Gap.png 746w, http://theorypc.ca/wp-content/uploads/2016/12/Big_Gap-300x145.png 300w" sizes="(max-width: 746px) 100vw, 746px" /> 

What we are looking at is two files that load on startup. Â SymELAM.sys and hwpolicy.sys and then a big gap, about 4 seconds before it starts the next process. Â What is SymELAM.sys and hwpolicy.sys? Â SymELAM is Symantec&#8217;s [Early Launch Anti Malware](https://msdn.microsoft.com/en-us/windows/hardware/drivers/install/early-launch-antimalware?f=255&MSPPError=-2147217396) driver. Â What does the ELAM do?

&#8220;..[This driver starts before other boot-start drivers and enables the evaluation of those drivers and helps the Windows kernel decide whether they should be initialized](https://msdn.microsoft.com/en-us/library/windows/desktop/hh848061(v=vs.85).aspx)..&#8221;

Could this be causing the delay? Â It&#8217;s certainly sounds like it&#8217;s possible. Â The ELAM loads, scans all other boot drivers and then continues booting could take 4 seconds.

Since this is PVS and objects will be carefully loaded into the image at predefined and planned times I would like to disable this driver. Â Unfortunately, I do not have access to the Symantec console at this time to disable it.

XPerf has another view we can use to see what activity it&#8217;s doing during this boot phase.

&#8220;Generic Events &#8211; Activity by Provider, Task, Opcode&#8221;

Looking at this view I can see 3 potential culprits that exist during this time period:

<img class="aligncenter size-full wp-image-1833" src="http://theorypc.ca/wp-content/uploads/2016/12/Culprits.png" alt="" width="950" height="643" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Culprits.png 950w, http://theorypc.ca/wp-content/uploads/2016/12/Culprits-300x203.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Culprits-768x520.png 768w" sizes="(max-width: 950px) 100vw, 950px" /> 

&nbsp;

EventMetaData, Microsoft-Windows-Kernel-PnP, and Microsoft-Windows-VolumeSnapshot-Driver. Â It&#8217;s possible they are all tied together somehow. Â The Microsoft-Windows-Kernel-PNP can be expanded so we can dig a little deeper into the events it generated.

Within it, we see there is a &#8216;BootDriverReinit&#8217; that spans that gap exactly.

<img class="aligncenter size-full wp-image-1834" src="http://theorypc.ca/wp-content/uploads/2016/12/BootDriver_Reinit.png" alt="" width="803" height="662" srcset="http://theorypc.ca/wp-content/uploads/2016/12/BootDriver_Reinit.png 803w, http://theorypc.ca/wp-content/uploads/2016/12/BootDriver_Reinit-300x247.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/BootDriver_Reinit-768x633.png 768w" sizes="(max-width: 803px) 100vw, 803px" /> 

Unfortunately, that&#8217;s all the information I get from BootDriverReinit.

The other culprit that also spans that time exactly is the &#8220;Microsoft-Windows-VolumeSnapshot-Driver&#8221;.

<img class="aligncenter size-full wp-image-1835" src="http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver.png" alt="" width="803" height="674" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver.png 803w, http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver-300x252.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver-768x645.png 768w" sizes="(max-width: 803px) 100vw, 803px" /> 

It&#8217;s event is completely blank though. Â It gives no details or hints for what it&#8217;s doing. Â However, it does give us a name that we can take a look at EventViewer and see if it recorded some Event Tracing for Windows events (ETW):

<img class="aligncenter size-full wp-image-1836" src="http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver_log.png" alt="" width="1031" height="732" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver_log.png 1031w, http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver_log-300x213.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Volume_Snapshot_Driver_log-768x545.png 768w" sizes="(max-width: 1031px) 100vw, 1031px" /> 

&nbsp;

Notice there is a lovely 4 second gap.

So&#8230; Is it VolumeSnapshot-Driver causing my delay? Â Looking at the events there are only two that cycle, 100 and 101. Â What do they mean:

<img class="aligncenter size-full wp-image-1837" src="http://theorypc.ca/wp-content/uploads/2016/12/100.png" alt="" width="540" height="258" srcset="http://theorypc.ca/wp-content/uploads/2016/12/100.png 540w, http://theorypc.ca/wp-content/uploads/2016/12/100-300x143.png 300w" sizes="(max-width: 540px) 100vw, 540px" /> 

<img class="aligncenter size-large wp-image-1838" src="http://theorypc.ca/wp-content/uploads/2016/12/101.png" alt="" width="536" height="253" srcset="http://theorypc.ca/wp-content/uploads/2016/12/101.png 536w, http://theorypc.ca/wp-content/uploads/2016/12/101-300x142.png 300w" sizes="(max-width: 536px) 100vw, 536px" /> 

It&#8217;s hard to say why it&#8217;s &#8220;Reinit&#8221;&#8230; Â It&#8217;s not even clear that it&#8217;s the volume snapshot driver causing my issue. Â The volume snapshot driver is kind of implying that the drive is being set offline then back online.

We can enable the analytic logs for the Volume Snapshot driver to see if it give more info. Â However, I&#8217;m going to disable the Volume Snapshot driver as our PVS environment has no use for VolumeSnapshots. Â How can we do that? Â In the registry we need to go to **HKLM\System\CurrentControlSet\Control\Class\{71a27cdd-812a-11d0-bec7-08002be2092f}** and remove &#8216;**volsnap**&#8216; from the **UpperFilters**:  
<img class="aligncenter size-large wp-image-1840" src="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_reg.png" alt="" width="882" height="727" srcset="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_reg.png 882w, http://theorypc.ca/wp-content/uploads/2016/12/volsnap_reg-300x247.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/volsnap_reg-768x633.png 768w" sizes="(max-width: 882px) 100vw, 882px" /> 

<p style="text-align: center;">
  To:
</p>

<img class="aligncenter size-full wp-image-1839" src="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_gone.png" alt="" width="529" height="204" srcset="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_gone.png 529w, http://theorypc.ca/wp-content/uploads/2016/12/volsnap_gone-300x116.png 300w" sizes="(max-width: 529px) 100vw, 529px" /> 

And I&#8217;ll disable the associated service with volsnap:

<div id="attachment_1841" style="width: 1016px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1841" class="size-full wp-image-1841" src="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_disabled.png" alt="" width="1006" height="734" srcset="http://theorypc.ca/wp-content/uploads/2016/12/volsnap_disabled.png 1006w, http://theorypc.ca/wp-content/uploads/2016/12/volsnap_disabled-300x219.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/volsnap_disabled-768x560.png 768w" sizes="(max-width: 1006px) 100vw, 1006px" /></p> 
  
  <p id="caption-attachment-1841" class="wp-caption-text">
    Original Value 0x0, 0x4 = disabled
  </p>
</div>

Did it help?

<img class="aligncenter size-full wp-image-1842" src="http://theorypc.ca/wp-content/uploads/2016/12/VolumeSnapshot_Gone.png" alt="" width="551" height="656" srcset="http://theorypc.ca/wp-content/uploads/2016/12/VolumeSnapshot_Gone.png 551w, http://theorypc.ca/wp-content/uploads/2016/12/VolumeSnapshot_Gone-252x300.png 252w" sizes="(max-width: 551px) 100vw, 551px" /> 

Well&#8230; The Volume Snapshot Driver is GONE, but we still see this ~4 second gap.

<img class="aligncenter size-full wp-image-1843" src="http://theorypc.ca/wp-content/uploads/2016/12/PNP.png" alt="" width="631" height="733" srcset="http://theorypc.ca/wp-content/uploads/2016/12/PNP.png 631w, http://theorypc.ca/wp-content/uploads/2016/12/PNP-258x300.png 258w" sizes="(max-width: 631px) 100vw, 631px" /> 

And we still see the BootDriverReinit spans that gap exactly. Â So we need more info on what&#8217;s happening here. Â Fortunately, debug logging can be enabled for Kernel-PNP:

<img class="aligncenter size-full wp-image-1844" src="http://theorypc.ca/wp-content/uploads/2016/12/Kernel-PNP.png" alt="" width="320" height="254" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Kernel-PNP.png 320w, http://theorypc.ca/wp-content/uploads/2016/12/Kernel-PNP-300x238.png 300w" sizes="(max-width: 320px) 100vw, 320px" /> 

&nbsp;

&nbsp;

I enabled it and I saw the following:

<img class="aligncenter size-full wp-image-1845" src="http://theorypc.ca/wp-content/uploads/2016/12/Boot_Reinit_3s_gap.png" alt="" width="716" height="83" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Boot_Reinit_3s_gap.png 716w, http://theorypc.ca/wp-content/uploads/2016/12/Boot_Reinit_3s_gap-300x35.png 300w" sizes="(max-width: 716px) 100vw, 716px" /> 

&nbsp;

A 3 second gap. Â What are those events (264 and 265)?

<img class="aligncenter size-full wp-image-1846" src="http://theorypc.ca/wp-content/uploads/2016/12/264.png" alt="" width="545" height="128" srcset="http://theorypc.ca/wp-content/uploads/2016/12/264.png 545w, http://theorypc.ca/wp-content/uploads/2016/12/264-300x70.png 300w" sizes="(max-width: 545px) 100vw, 545px" /> 

&nbsp;

Well.. There is our BootReinit. Â And 265?

<img class="aligncenter size-full wp-image-1848" src="http://theorypc.ca/wp-content/uploads/2016/12/265.png" alt="" width="508" height="142" srcset="http://theorypc.ca/wp-content/uploads/2016/12/265.png 508w, http://theorypc.ca/wp-content/uploads/2016/12/265-300x84.png 300w" sizes="(max-width: 508px) 100vw, 508px" /> 

&nbsp;

This gap is also more accurately depicted in theÂ &#8220;Boot Diagnostic Channel&#8221;

<img class="aligncenter size-full wp-image-1849" src="http://theorypc.ca/wp-content/uploads/2016/12/boot_diagnostic_channel.png" alt="" width="665" height="216" srcset="http://theorypc.ca/wp-content/uploads/2016/12/boot_diagnostic_channel.png 665w, http://theorypc.ca/wp-content/uploads/2016/12/boot_diagnostic_channel-300x97.png 300w" sizes="(max-width: 665px) 100vw, 665px" /> 

&nbsp;

(events 200 and 201)

<img class="aligncenter size-full wp-image-1850" src="http://theorypc.ca/wp-content/uploads/2016/12/bootphase.png" alt="" width="817" height="211" srcset="http://theorypc.ca/wp-content/uploads/2016/12/bootphase.png 817w, http://theorypc.ca/wp-content/uploads/2016/12/bootphase-300x77.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/bootphase-768x198.png 768w" sizes="(max-width: 817px) 100vw, 817px" /> 

These events take up the 5 seconds I&#8217;m trying to get rid of.

&nbsp;

So is the Boot Reinit phase something I can do something about? [Â My answer appears to be here.](https://msdn.microsoft.com/en-us/windows/hardware/drivers/ifs/what-happens-to-file-systems-during-system-boot)

  1. After all drivers that load at boot time have been initialized, the I/O Manager calls the reinitialization routines of any drivers that have them. A _reinitialization routine_ is a callback routine that is registered by a boot driver that needs to be given additional processing time at this point in the boot process. Reinitialization routines are registered by calling [**IoRegisterBootDriverReinitialization**](https://msdn.microsoft.com/library/windows/hardware/ff549494) or [**IoRegisterDriverReinitialization**](https://msdn.microsoft.com/library/windows/hardware/ff549511)

So&#8230;

It appears I need find any drivers that call the reinitialization routine and see what I have. Â I don&#8217;t know if the reinitialization routine is event driven, but it doesn&#8217;t \*seem\* like it.

I wrote a quick and dirty script that looks within the binary of the boot drivers to try and find those API calls. Â If those API calls are not stored as a string within the driver than I&#8217;ll have missed some:

<pre class="lang:ps decode:true">cd HKLM:\SYSTEM\CurrentControlSet\Services

$services = ls

$bootStartService = @()

#enumerating all boot drivers (0x0) of start type 
&lt;#--
https://msdn.microsoft.com/en-us/windows/hardware/drivers/ifs/what-determines-when-a-driver-is-loaded
SERVICE_BOOT_START (0x00000000)
Indicates a driver started by the operating system (OS) loader. File system filter drivers commonly use this start type or SERVICE_DEMAND_START. On Microsoft Windows XP and later systems, filters must use this start type in order to take advantage of the new file system filter load order groups.

SERVICE_SYSTEM_START (0x00000001)
Indicates a driver started during OS initialization. This start type is used by the file system recognizer. Except for the file systems listed below under "SERVICE_DISABLED," file systems (including network file system components) commonly use this start type or SERVICE_DEMAND_START. This start type is also used by device drivers for PnP devices that are enumerated during system initialization but not required to load the system.

SERVICE_AUTO_START (0x00000002)
Indicates a driver started by the Service Control Manager during system startup. Rarely used.

SERVICE_DEMAND_START (0x00000003)
Indicates a driver started on demand, either by the PnP Manager (for device drivers) or by the Service Control Manager (for file systems and file system filter drivers).

SERVICE_DISABLED (0x00000004)
Indicates a driver that is not started by the OS loader, Service Control Manager, or PnP Manager. Used by file systems that are loaded by a file system recognizer (except when they are the boot file system) or (in the case of EFS) by another file system. Such file systems include CDFS, EFS, FastFat, NTFS, and UDFS. Also used to temporarily disable a driver during debugging.
--#&gt;

write-host "Boot Drivers that reinit:"

foreach ($service in $services) {
 $path = $service.GetValue("ImagePath")
 if ($service.GetValue("Start")-eq 0) {
 $string = \\jeeves\~trententtye\pstools\strings.exe "C:\Windows\$path"
 if ($string -match "IoRegisterBootDriverReinitialization") {
 #$string -match "IoRegisterBootDriverReinitialization"
 write-host $service.PSChildName
 }
 if ($string -match "IoRegisterDriverReinitialization") {
 #$string -match "IoRegisterDriverReinitialization"
 write-host $service.PSChildName
 }
 }
}
</pre>

My results:

<pre class="lang:default decode:true ">Drivers that reinit:
ACPI
CtxMcsWbc - Citrix Machine Creation Services Write Back Cache
CVhdMp    - Citrix Virtual Hard Disk Adapter
DAFsFilter - Citrix AppDisk Driver
disk
FltMgr
fvevol     - BitLocker Encryption Driver
hvservice  - HyperVisor Boot Driver
mountmgr
NDIS
VerifierExt - Driver Verifier 
volmgr
volsnap     - Volume Shadow Copy driver
WindowsTrustedRTProxy - Windows Trusted Service Runtime Proxy Driver</pre>

<pre class="lang:ps decode:true">Boot Drivers that reinit:

ACPI
CtxMcsWbc
CVhdMp
disk
FltMgr
fvevol
mountmgr
NDIS
volmgr
WindowsTrustedRTProxy</pre>

I&#8217;d like to eliminate as many of these as possible&#8230; Â Volume Shadow Copy can be removed on PVS boxes, Bitlocker&#8230; Â Is the HyperVisor boot driver only relevant to Hyper-V I wonder, or does VMWare need it?

&nbsp;

To continue, I will remove BitLocker.

  1. I&#8217;ll remove it from the &#8216;Volume&#8217; Classes to prevent it from attaching to any volume:  
<img class="aligncenter size-full wp-image-1852" src="http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot.png" alt="" width="872" height="321" srcset="http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot.png 872w, http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot-300x110.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot-768x283.png 768w" sizes="(max-width: 872px) 100vw, 872px" /><img class="aligncenter size-large wp-image-1853" src="http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot_done.png" alt="" width="853" height="318" srcset="http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot_done.png 853w, http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot_done-300x112.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/remove_bitlocker_rdyboot_done-768x286.png 768w" sizes="(max-width: 853px) 100vw, 853px" />  
    I removed &#8216;[rdyboost](https://technet.microsoft.com/en-us/library/ff356869.aspx)&#8216; as well since it&#8217;s not applicable to a PVS environment either.

Did these tweaks help?

Well, BootDriverReinit now says it starts in milliseconds as opposed to seconds:

<img class="aligncenter size-full wp-image-1854" src="http://theorypc.ca/wp-content/uploads/2016/12/BootDriverReinit.png" alt="" width="657" height="419" srcset="http://theorypc.ca/wp-content/uploads/2016/12/BootDriverReinit.png 657w, http://theorypc.ca/wp-content/uploads/2016/12/BootDriverReinit-300x191.png 300w" sizes="(max-width: 657px) 100vw, 657px" /> 

But my Pre-Session Init time is still around 6 seconds and there is still a gap.

I added some options to my xbootmgr.exe:

<pre class="lang:default decode:true ">"C:\Program Files (x86)\Windows Kits\10\Windows Performance Toolkit\xbootmgr.exe" -trace boot -traceflags base+cswitch+drivers+power+latency+dispatcher -stackwalk profile+cswitch+readythread -notraceflagsinfilename -postbootdelay 10</pre>

And re-examined the events in the Performance Analyzer:

<img class="aligncenter size-full wp-image-1855" src="http://theorypc.ca/wp-content/uploads/2016/12/bnistack6.png" alt="" width="673" height="893" srcset="http://theorypc.ca/wp-content/uploads/2016/12/bnistack6.png 673w, http://theorypc.ca/wp-content/uploads/2016/12/bnistack6-226x300.png 226w" sizes="(max-width: 673px) 100vw, 673px" /> 

And I saw the longest time taken for the Pre Session Init is now bnistack6.sys. Â BNIStack6.sys is the Citrix driver that connects to PVS. Â I&#8217;m curious why this is 3 seconds long and if it can be shortened. Â Citrix has a [PVS boot process guide here](https://support.citrix.com/content/dam/supportWS/kA460000000CcClCAK/Provisioning_Services_Boot_Process.pdf).

At this point of the boot process I believe we are points 3-5:

3. PVS Logon Process â€“ The Target Device logs on to PVS.  
4. Single Read Mode â€“ Single read mode communication is established between the Target Device and the PVS Server.  
5. BNISTACK / MIO â€“ The BNISTACK driver on the Target Device takes over communications with the PVS Server and Muliple I/O occurs

What I&#8217;m going to do is install CDFControl and procmon on the PVS server, and get the Target Device to &#8220;wait&#8221; at the boot selection screen (have it set to maintenance mode). Â I&#8217;m then going to start CDFControl and procmon to see what is happening to cause this 3 second delay. Â I can determine the timeline of events with the event viewer on the target device. The 3 second delay is visible with the &#8220;bnistack&#8221; event and when the next event kicks off (NTFS).

<div id="attachment_1856" style="width: 704px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1856" class="wp-image-1856 size-full" src="http://theorypc.ca/wp-content/uploads/2016/12/waiting_for_boot.png" width="694" height="557" srcset="http://theorypc.ca/wp-content/uploads/2016/12/waiting_for_boot.png 694w, http://theorypc.ca/wp-content/uploads/2016/12/waiting_for_boot-300x241.png 300w" sizes="(max-width: 694px) 100vw, 694px" /></p> 
  
  <p id="caption-attachment-1856" class="wp-caption-text">
    We wait to set our monitoring at this stage&#8230;
  </p>
</div>

&nbsp;

And then start tracing&#8230;

<img class="aligncenter size-large wp-image-1857" src="http://theorypc.ca/wp-content/uploads/2016/12/start_tracing-1600x754.png" alt="" width="1140" height="537" srcset="http://theorypc.ca/wp-content/uploads/2016/12/start_tracing-1600x754.png 1600w, http://theorypc.ca/wp-content/uploads/2016/12/start_tracing-300x141.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/start_tracing-768x362.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

Now we need to establish our timeline. Â Examining the SYSTEM event log we can see when bnistack starts and the delay from when the next action starts (Ntfs):

<img class="aligncenter size-full wp-image-1858" src="http://theorypc.ca/wp-content/uploads/2016/12/bnistack_delay.png" alt="" width="976" height="335" srcset="http://theorypc.ca/wp-content/uploads/2016/12/bnistack_delay.png 976w, http://theorypc.ca/wp-content/uploads/2016/12/bnistack_delay-300x103.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/bnistack_delay-768x264.png 768w" sizes="(max-width: 976px) 100vw, 976px" /> 

Our timeline of events are 1:09:17-1:09:21, about 4 seconds. Â We started tracing on the PVS server to determine if the delay is caused by the PVS server. Â If we don&#8217;t see anything that jumps out at us then the delay maybe on the client end which could be harder to work with.

In order to minimize noise, this PVS environment has a single vDisk with a single booted target device. Â I highlighted in yellow all the events that occured during our time line.

<img class="aligncenter size-large wp-image-1860" src="http://theorypc.ca/wp-content/uploads/2016/12/CDFControl-1600x736.png" alt="" width="1140" height="524" srcset="http://theorypc.ca/wp-content/uploads/2016/12/CDFControl-1600x736.png 1600w, http://theorypc.ca/wp-content/uploads/2016/12/CDFControl-300x138.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/CDFControl-768x353.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

We can see that the PVS server is operating and doing \*something\* during boot. Â I tried this boot process a few times and the events are the same implying that the BNIStack maybe waiting for a response from the PVS Server before continuing to boot. Â Unfortunately, some of the TMF files are not present in Citrix&#8217;s public respository so these are private and undecipherable. Â We can see process ID&#8217;s (1956 and 1492) and it seems to cycle from 1492 -> 1956 -> 1492. Â The total length of time is 2.6 seconds. Â Hopefully, procmon can give us some more information to what is going on.

Process 1492 is the Stream Process and process 1956 is the Notifier process.

<img class="aligncenter size-full wp-image-1861" src="http://theorypc.ca/wp-content/uploads/2016/12/1956.png" alt="" width="169" height="36" /><img class="aligncenter size-large wp-image-1862" src="http://theorypc.ca/wp-content/uploads/2016/12/1492.png" alt="" width="168" height="35" /> 

Procmon trace of the time period:

<img class="aligncenter size-large wp-image-1863" src="http://theorypc.ca/wp-content/uploads/2016/12/procmon1-1412x1600.png" alt="" width="1140" height="1292" srcset="http://theorypc.ca/wp-content/uploads/2016/12/procmon1-1412x1600.png 1412w, http://theorypc.ca/wp-content/uploads/2016/12/procmon1-265x300.png 265w, http://theorypc.ca/wp-content/uploads/2016/12/procmon1-768x870.png 768w, http://theorypc.ca/wp-content/uploads/2016/12/procmon1.png 1588w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

We can see it&#8217;s communicating with the database during this time. Â Could it be blocking on updating or receiving information from the database? Â The notifier service is no where to be found during this time, according to procmon.

If we exclude the registry and file system, which focus only on SQL client components, to look at the network we see the following:

<img class="aligncenter size-large wp-image-1864" src="http://theorypc.ca/wp-content/uploads/2016/12/procmon2-1566x1600.png" alt="" width="1140" height="1165" srcset="http://theorypc.ca/wp-content/uploads/2016/12/procmon2-1566x1600.png 1566w, http://theorypc.ca/wp-content/uploads/2016/12/procmon2-294x300.png 294w, http://theorypc.ca/wp-content/uploads/2016/12/procmon2-768x784.png 768w, http://theorypc.ca/wp-content/uploads/2016/12/procmon2-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2016/12/procmon2.png 1588w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

We see if purely communicating with the database server during this time period. Â It starts talking to the target device at the start of the UDP traffic (1:09:19.99) and seems to really kick off at 1:09:20.01 after the last discussion with the database.

The first bits of UDP traffic appear to be it negotiating what port (or maybe someother details). Â If I enable the file system monitor I can see the first read of the vDisk occurs at 1:09:20.54 and then the streaming really begins. Â So far after the database is done talking.

So what is it saying? Â I installed [Microsoft Network Monitor](https://www.google.ca/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjfl5eEgJrRAhWE54MKHUNFC64QFggwMAA&url=https%3A%2F%2Fwww.microsoft.com%2Fen-ca%2Fdownload%2Fdetails.aspx%3Fid%3D4865&usg=AFQjCNE8UDy1HpTP2dx3AfuilqmQvw_wpA&sig2=hxklRdD30Q9-UY7vSqEBoA&bvm=bv.142059868,d.amc) and [Message Analyzer](https://www.google.ca/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&sqi=2&ved=0ahUKEwi9yMOLgJrRAhUJwYMKHS5IAn0QFggaMAA&url=https%3A%2F%2Fwww.microsoft.com%2Fen-ca%2Fdownload%2Fdetails.aspx%3Fid%3D44226&usg=AFQjCNGDy8bs9bEBNHDRr_BqGg2Fuud4ew&sig2=Z2QxUSP_mtqGQ2FEXdElNw&bvm=bv.142059868,d.amc)Â and traced the communication.

I did the trace in Microsoft Network Monitor because I find it quite a bit faster than Message Analyzer. Â I saved the CAP file and then opened it in Message Analyzer where I changed the traffic Module to TDS (SQL traffic).

For this trace my bootup time was 10:10:08 -> 10:10:12

<img class="aligncenter size-full wp-image-1866" src="http://theorypc.ca/wp-content/uploads/2016/12/trace2.png" alt="" width="614" height="100" srcset="http://theorypc.ca/wp-content/uploads/2016/12/trace2.png 614w, http://theorypc.ca/wp-content/uploads/2016/12/trace2-300x49.png 300w" sizes="(max-width: 614px) 100vw, 614px" /> 

&nbsp;

I created a filter for the SQL server and saved my packet capture from MS NetMon:

<img class="aligncenter size-full wp-image-1867" src="http://theorypc.ca/wp-content/uploads/2016/12/NetMon.png" alt="" width="1387" height="813" srcset="http://theorypc.ca/wp-content/uploads/2016/12/NetMon.png 1387w, http://theorypc.ca/wp-content/uploads/2016/12/NetMon-300x176.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/NetMon-768x450.png 768w" sizes="(max-width: 1387px) 100vw, 1387px" /> 

&nbsp;

I opened it in Message Analyzer and set the Module to TDS:

<img class="aligncenter size-large wp-image-1868" src="http://theorypc.ca/wp-content/uploads/2016/12/Message_Analyzer-1600x1297.png" alt="" width="1140" height="924" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Message_Analyzer-1600x1297.png 1600w, http://theorypc.ca/wp-content/uploads/2016/12/Message_Analyzer-300x243.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Message_Analyzer-768x622.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

This now showed me all the conversations to the DB server and the &#8216;time delta&#8217; (eg, either how long for a response or to formulate another request).

We are mostly interested in the small time window of 10:07 -> 10:11 as the system continued booting at 10:12, so I believe we can ignore the SoapServer.exe and Notifier.exe as both start after 10:12.

To present this data better, we now find a packet with data or a command in the ASCII window and drill down into it. Â Once we find the data/command I right-clicked and selected &#8220;Add &#8216;data&#8217; as Column&#8221;.

<img class="aligncenter size-large wp-image-1869" src="http://theorypc.ca/wp-content/uploads/2016/12/add-as-column-1600x624.png" alt="" width="1140" height="445" srcset="http://theorypc.ca/wp-content/uploads/2016/12/add-as-column-1600x624.png 1600w, http://theorypc.ca/wp-content/uploads/2016/12/add-as-column-300x117.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/add-as-column-768x300.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

And now we can view our SQL messages:

<img class="aligncenter size-large wp-image-1870" src="http://theorypc.ca/wp-content/uploads/2016/12/SQL_Messages-1600x647.png" alt="" width="1140" height="461" srcset="http://theorypc.ca/wp-content/uploads/2016/12/SQL_Messages-1600x647.png 1600w, http://theorypc.ca/wp-content/uploads/2016/12/SQL_Messages-300x121.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/SQL_Messages-768x311.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

In plain text the messages are:

<img class="aligncenter size-large wp-image-2011" src="http://theorypc.ca/wp-content/uploads/2017/01/DB_Calls-1600x859.png" alt="" width="1140" height="612" srcset="http://theorypc.ca/wp-content/uploads/2017/01/DB_Calls-1600x859.png 1600w, http://theorypc.ca/wp-content/uploads/2017/01/DB_Calls-300x161.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/DB_Calls-768x412.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

&nbsp;

When I take those commands and execute them on the DB server to see if the server is causing my latency, I see the following:  
<img class="aligncenter size-full wp-image-1909" src="http://theorypc.ca/wp-content/uploads/2017/01/SQL_Elapsed_Time.png" alt="" width="1593" height="1132" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SQL_Elapsed_Time.png 1593w, http://theorypc.ca/wp-content/uploads/2017/01/SQL_Elapsed_Time-300x213.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/SQL_Elapsed_Time-768x546.png 768w" sizes="(max-width: 1593px) 100vw, 1593px" />  
SQL has no problems executing any of these queries in just a few milliseconds. So SQL is operating fast and properly. Â Is it the communication between the PVS box and SQL? Â I copied those commands into a text file:

<pre class="lang:tsql decode:true">EXEC [dbo].[uspSetServerStatusContactInfo] @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f', @ip = -1062731508, @port = 6910, @deviceCount = 0, @powerRating = 1.000000, @siteId = '79e9786a-5d92-411c-907d-e1f52b0430bd', @serverCacheTimeout = 8 
EXEC [dbo].[uspGetDeviceByMac] @deviceMac = '00155DA5DD1C' 
EXEC [dbo].[uspGetDiskVersionsForProductionDevice] @deviceIntId = 2 
EXEC [dbo].[uspGetDeviceBootstraps] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80' 
"EXEC [dbo].[uspGetDiskTempVersionForDevice] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80'"
EXEC [dbo].[uspGetActiveServersForDiskLocatorGuid] @diskLocatorId = '96202eef-453f-4ef1-aa1b-6b69a0a4ebad' 
EXEC [dbo].[uspIncrementServerStatusDeviceCount] @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f' 
EXEC [dbo].[uspGetDeviceByIdRev1] @id = 2 
EXEC [dbo].[uspGetDiskVersionInfo] @diskId = 2, @diskVersion = 2, @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f' 
EXEC [dbo].[uspSetServerStatusDeviceCount] @deviceCount = 1, @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f' 
EXEC [dbo].[uspSetServerStatusContactInfo] @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f', @ip = -1062731508, @port = 6910, @deviceCount = 1, @powerRating = 1.000000, @siteId = '79e9786a-5d92-411c-907d-e1f52b0430bd', @serverCacheTimeout = 8 
EXEC [dbo].[uspGetDeviceStatusByGuid] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80' 
"DECLARE @makLicenseActivated tinyint, @makUsedToActivate nvarchar(128), @proxyActivationConfirmationId nvarchar(128) EXEC [dbo].[uspAddDeviceStatusAndReturnMakActivationInfoRev1] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80', @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f', @diskLocatorId = '96202eef-453f-4ef1-aa1b-6b69a0a4ebad', @ip = -1062731501, @serverPortConnection = 6914, @serverIpConnection = -1062731508, @licenseMask = 0, @diskVersion = 2, @diskLicenseMode = 0, @makLicenseActivated = @makLicenseActivated OUTPUT, @makUsedToActivate = @makUsedToActivate OUTPUT, @proxyActivationConfirmationId = @proxyActivationConfirmationId OUTPUT SELECT @makLicenseActivated as N'makLicenseActivated', @makUsedToActivate as N'makUsedToActivate', @proxyActivationConfirmationId as N'proxyActivationConfirmationId' "
EXEC [dbo].[uspGetDevicePersonalityAndObjectsRev1] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80' 
EXEC [dbo].[uspSetServerStatusContactInfo] @serverId = '2cd2f78c-18d8-4561-a772-c33f6ca3740f', @ip = -1062731508, @port = 6910, @deviceCount = 1, @powerRating = 1.000000, @siteId = '79e9786a-5d92-411c-907d-e1f52b0430bd', @serverCacheTimeout = 8 
EXEC [dbo].[uspGetDeviceImagingWizardContinue] @deviceId = 'b28e6999-ac4f-4d21-a79c-2a617a0b1e80' 
</pre>

then copied SQLCMD.exe from a SQL2012 installation (the SQL Native Client 11 that PVS uses is a 2012 product so the SQLCMD.exe needs to match that version to use the SQL driver) and created a powershell script to measure how long it takes to execute ALL of these commands with returned output:

<pre class="lang:batch decode:true">$commands = get-content db_commands.txt

write-host "All Commands Execution Time (milliseconds):"

(measure-command { 
  foreach ($command in $commands) {
    ."C:\Users\amttye\Desktop\SQLCMD\SQLCMD.EXE" -S SQL2016.bottheory.local -d "ProvisioningServices2016" -Q "$command"
  }
}).TotalMilliseconds

write-host "Each Command (milliseconds)"
foreach ($command in $commands) {
  (measure-command {
    ."C:\Users\amttye\Desktop\SQLCMD\SQLCMD.EXE" -S SQL2016.bottheory.local -d "ProvisioningServices2016" -Q "$command"
  }).TotalMilliseconds
}</pre>

This was how long it took to execute those commands:

<pre class="lang:default decode:true">All Commands Execution Time (milliseconds):
670.1241
Each Command (milliseconds)
43.7516
39.589
45.185
38.779
40.6855
40.5374
38.7132
39.8786
41.6513
42.5581
46.7282
43.4835
43.7464
46.8148
42.8088
42.9161</pre>

All of those commands combined only add 670 milliseconds.

The SQL server is responding and getting back to the PVS server with an average return of ~45ms per query. Â The SQL actions and responses are fast.

Long story short. Â I have made no progress on determining what is happening during these ~3-4 seconds. Â To validate that this is NOT something with my environment, I built a whole new PVS 7.12 environment in my home lab. Â The results?

<img class="aligncenter size-full wp-image-2008" src="http://theorypc.ca/wp-content/uploads/2017/01/bnistack6_3.3_2.png" alt="" width="1020" height="718" srcset="http://theorypc.ca/wp-content/uploads/2017/01/bnistack6_3.3_2.png 1020w, http://theorypc.ca/wp-content/uploads/2017/01/bnistack6_3.3_2-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/bnistack6_3.3_2-768x541.png 768w" sizes="(max-width: 1020px) 100vw, 1020px" /> 

bnistack6.sys takes 3.6 seconds. Â There doesn&#8217;t appear to be anyways to reduce this. Â At a minimum, using PVS will add 3.0+ seconds to your boot time at a minimum.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->