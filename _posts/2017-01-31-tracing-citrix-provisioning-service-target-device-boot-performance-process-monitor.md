---
id: 1986
title: Tracing Citrix Provisioning Service (PVS) Target Device Boot Performance - Process Monitor
date: 2017-01-31T07:33:10-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1986
permalink: /2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-process-monitor/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2017/01/Inject_Procmon-1.mp4
    190
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_PVS_BootLogging-1.mp4
    199
    video/mp4
    
image: /wp-content/uploads/2017/01/Procmon_Tree.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - Provisioning Services
  - PVS
  - Registry
  - Target Device
  - Windows 10
---
Non-Persistent Citrix PVS Target Devices have more complicated boot processes then a standard VM.  This is because the Citrix PVS server components play a big role in acting as the boot disk.  They send UDP packets over the network to the target device.  This adds a delay that you simply cannot avoid (albeit, possibly a small one but there is no denying network communication should be slower than a local hard disk/SSD).

One of the things we can do is set the PVS target devices up in such a way that we can get real, measurable data on what the target device is doing while it's booting.  This will give us visibility into what we may actually require for our target devices.

There are two programs that I use to measure boot performance.  [Windows Performance Toolkit](https://msdn.microsoft.com/en-us/windows/hardware/commercialize/test/wpt/index) and [Process Monitor](https://technet.microsoft.com/en-us/sysinternals/processmonitor.aspx).  I would not recommend running both at the same time because the logging does add some overhead (especially procmon in my humble experience).

The next bit of this post will detail how to offline inject the necessary software and tools into your target device image to begin capturing boot performance data.

# Process Monitor

For Process Monitor you must extract the boot driver and inject the process monitor executable itself into the image.

To extract the boot driver simple launch process monitor, under the Options menu, select 'Enable Boot Logging'

<img class="aligncenter size-full wp-image-1988" src="http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_BootLogging-1.png" alt="" width="590" height="326" srcset="http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_BootLogging-1.png 590w, http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_BootLogging-1-300x166.png 300w" sizes="(max-width: 590px) 100vw, 590px" /> 

Then browse to your C:\Windows\System32\Drivers folder, and with "Show Hidden Files" enabled, copy out Procmon23.sys

<img class="aligncenter size-full wp-image-1989" src="http://theorypc.ca/wp-content/uploads/2017/01/Procmon23sys.png" alt="" width="817" height="472" srcset="http://theorypc.ca/wp-content/uploads/2017/01/Procmon23sys.png 817w, http://theorypc.ca/wp-content/uploads/2017/01/Procmon23sys-300x173.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/Procmon23sys-768x444.png 768w" sizes="(max-width: 817px) 100vw, 817px" /> 

It might be a good idea to disable boot logging if you did it on your personal system now 

&nbsp;

Now we need to inject the follow registry entry into our image:

<pre class="lang:reg decode:true">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\services\PROCMON23]
"SupportedFeatures"=dword:00000003
"Start"=dword:00000000
"Group"="FSFilter Activity Monitor"
"Type"=dword:00000001
"ImagePath"=hex(2):53,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,44,00,\
 72,00,69,00,76,00,65,00,72,00,73,00,5c,00,50,00,52,00,4f,00,43,00,4d,00,4f,\
 00,4e,00,32,00,33,00,2e,00,53,00,59,00,53,00,00,00

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\services\PROCMON23\Instances]
"DefaultInstance"="Process Monitor 23 Instance"

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\services\PROCMON23\Instances\Process Monitor 23 Instance]
"Altitude"="385200"
"Flags"=dword:00000000
</pre>

Here are the steps in action:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1986-18" width="1140" height="712" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/Inject_Procmon-1.mp4?_=18" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/Inject_Procmon-1.mp4">http://theorypc.ca/wp-content/uploads/2017/01/Inject_Procmon-1.mp4</a></video>
</div>

Seal/promote the image.

On next boot you will have captured boot information:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1986-19" width="1140" height="944" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_PVS_BootLogging-1.mp4?_=19" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_PVS_BootLogging-1.mp4">http://theorypc.ca/wp-content/uploads/2017/01/ProcMon_PVS_BootLogging-1.mp4</a></video>
</div>

To see how to use [Windows Performance Toolkit for boot tracing Citrix PVS Target Device's click here.](https://theorypc.ca/2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-windows-performance-toolkit/)

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->