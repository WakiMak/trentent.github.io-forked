---
id: 1981
title: 'Tracing Citrix Provisioning Service (PVS) Target Device Boot Performance - Windows Performance Toolkit'
date: 2017-01-31T07:31:46-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1981
permalink: /2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-windows-performance-toolkit/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2017/01/Injecting_WPT-1.mp4
    189
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2017/01/WPT_In_Action-1.mp4
    189
    video/mp4
    
image: /wp-content/uploads/2017/01/WPT.png
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
Non-Persistent Citrix PVS Target Devices have more complicated boot processes then a standard VM. Â This is because the Citrix PVS server components play a big role in acting as the boot disk. Â They send UDP packets over the network to the target device. Â This adds a delay that you simply cannot avoid (albeit, possibly a small one but there is no denying network communication should be slower than a local hard disk/SSD).

One of the things we can do is set the PVS target devices up in such a way that we can get real, measurable data on what the target device is doing while it's booting. Â This will give us visibility into what we may actually require for our target devices.

There are two programs that I use to measure boot performance. Â [Windows Performance Toolkit](https://msdn.microsoft.com/en-us/windows/hardware/commercialize/test/wpt/index) and [Process Monitor](https://technet.microsoft.com/en-us/sysinternals/processmonitor.aspx). Â I would not recommend running both at the same time because the logging does add some overhead (especially procmon in my humble experience).

The next bit of this post will detail how to offline inject the necessary software and tools into your target device image to begin capturing boot performance data.

# Windows Performance Toolkit

For the Windows Performance Toolkit it must be installed on the image or you can copy theÂ files from an install to your image in the following path:

<pre class="lang:reg decode:true">C:\Program Files (x86)\Windows Kits\10\Windows Performance Toolkit\</pre>

To offline inject, simply mount your vDisk image and copy the files there:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1981-16" width="1140" height="711" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/Injecting_WPT-1.mp4?_=16" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/Injecting_WPT-1.mp4">http://theorypc.ca/wp-content/uploads/2017/01/Injecting_WPT-1.mp4</a></video>
</div>

&nbsp;

Then the portion of it that we are interested in is "xbootmgr.exe" (aka boot logging). Â In order to enable boot logging we need to inject the following registry key into our PVS Image:

<pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\XPerfBootmgr]
"TraceMode"=dword:00000001
"RepeatCount"=dword:00000001
"RepeatIteration"=dword:00000001
"PostBootDelay"=dword:00002710
"TraceFlags"="base+latency+dispatcher"
"StackWalkingFilter"=hex:2e,0f,00,00,24,05,00,00,32,05,00,00,01,05,00,00
"ResultPath"="C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit"
"Callback"=""
"RunTag"=""
"SleeperCmd"="C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit\\xbootmgrSleep.exe"
"PreTraceCmd"=""
"PrepSystem"=dword:00000000
"IssueFlush"=dword:00000000
"UseLog"=dword:00000001
"LeavePremerged"=dword:00000000
"NoMerge"=dword:00000000
"TraceFlagsInFileName"=dword:00000000
"VerboseReadyBoot"=dword:00000000
"IsBuffering"=dword:00000000
"NumPrepSystemIterations"=dword:00000000
"DisablePopups"=dword:00000000



[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\GlobalLogger]
"Status"=dword:00000000
"Start"=dword:00000001
"FileName"="C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit\\boot_1_km_premerge.etl"
"EnableKernelFlags"=hex:07,23,00,00,86,42,88,00,00,00,00,00,00,00,00,00,00,00,\
 00,00,00,00,00,00,00,00,00,00,00,00,00,00
"StackWalkingFilter"=hex:2e,0f,00,00,24,05,00,00,32,05,00,00,01,05,00,00
"BufferSize"=dword:00000400
"MinimumBuffers"=dword:0000012c
"MaximumBuffers"=dword:00000258
"ClockType"=dword:00000001
"MaxFileSize"=dword:00000000
"LogFileMode"=dword:00000000

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\GlobalLogger\E51BB6E2-914A-4e21-93C0-192F4801BBFF]
"Flags"=dword:0000ffff
"Level"=dword:00000003



[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession]
"Start"=dword:00000001
"GUID"="{1a15901b-0686-4433-955f-2ee7f9c61cfc}"
"FileName"="C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit\\boot_1_um_premerge.etl"
"MinimumBuffers"=dword:00000020
"MaximumBuffers"=dword:00000020
"MaxFileSize"=dword:00000000
"BufferSize"=dword:00000400
"ClockType"=dword:00000001

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{0063715b-eeda-4007-9429-ad526f62696e}]
"ProviderFriendlyName"="Service Control Manager"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{030f2f57-abd0-4427-bcf1-3a3587d7dc7d}]
"ProviderFriendlyName"="PerfTrack Status"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{06184c97-5201-480e-92af-3a3626c5b140}]
"ProviderFriendlyName"="Service Host Provider"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{0a002690-3839-4e3a-b3b6-96d8df868d99}]
"ProviderFriendlyName"="AMP1"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{15ca44ff-4d7a-4baa-bba5-0998955e531e}]
"ProviderFriendlyName"="Loader Info"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{176cd9c5-c90c-5471-38ba-0eeb4f7e0bd0}]
"ProviderFriendlyName"="Microsoft.Windows.UI.Logon"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{1915117c-a61c-54d4-6548-56cac6dbfede}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.AboveLockActivationManager"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{1fd7c1d2-d037-4620-8d29-b2c7e5fcc13a}]
"ProviderFriendlyName"="Software Licensing (V6)"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{206f6dea-d3c5-4d10-bc72-989f03c8b84b}]
"ProviderFriendlyName"="Wininit"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{2a274310-42d5-4019-b816-e4b8c7abe95c}]
"ProviderFriendlyName"="ReadyBoost Driver"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):60,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{30336ed4-e327-447c-9de0-51b652c86108}]
"ProviderFriendlyName"="ShellCore"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{331c3b3a-2005-44c2-ac5e-77220c37d6b4}]
"ProviderFriendlyName"="Kernel-mode Power"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{3ec987dd-90e6-5877-ccb7-f27cdf6a976b}]
"ProviderFriendlyName"="Microsoft.Windows.LogonUI.WinlogonRPC"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{43e63da5-41d1-4fbf-aded-1bbed98fdd1d}]
"ProviderFriendlyName"="SMSS"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{49c2c27c-fe2d-40bf-8c4e-c3fb518037e7}]
"ProviderFriendlyName"="Search"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{4ee76bd8-3cf4-44a0-a0ac-3937643e37a3}]
"ProviderFriendlyName"="Code Integrity"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{4f7c073a-65bf-5045-7651-cc53bb272db5}]
"ProviderFriendlyName"="Microsoft.Windows.LogonController"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{5322d61a-9efa-4bc3-a3f9-14be95c144f8}]
"ProviderFriendlyName"="Prefetcher"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{56dc463b-97e8-4b59-e836-ab7c9bb96301}]
"ProviderFriendlyName"="DiagTrack Status"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{594bf743-ce2e-48ee-83ee-3d50a0add692}]
"ProviderFriendlyName"="Microsoft.Windows.AppModel.TileDataModel"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{63d2bb1d-e39a-41b8-9a3d-52dd06677588}]
"ProviderFriendlyName"="AuthUI"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{67fe2216-727a-40cb-94b2-c02211edb34a}]
"ProviderFriendlyName"="VolSnap"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{74cc4a0b-f577-5929-abcb-aa4bea374cb3}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.LockAppHost"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{751ef305-6c6e-4fed-b847-02ef79d26aef}]
"ProviderFriendlyName"="AMP3"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{8c416c79-d49b-4f01-a467-e56d3aa8234c}]
"ProviderFriendlyName"="DwmWin32kWin8"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,40,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{8e92deef-5e17-413b-b927-59b2f06a3cfc}]
"ProviderFriendlyName"="AMP2"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{96319132-2f52-5969-f14c-0d0a171b357a}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.LockFrameworkUAP"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{9c205a39-1250-487d-abd7-e831c6290539}]
"ProviderFriendlyName"="Kernel-mode PnP"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):20,f0,1f,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{9ca921e3-25a4-5d34-39da-a59bd8bdf7a2}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.LockAppBroker"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{9d55b53d-449b-4824-a637-24f9d69aa02f}]
"ProviderFriendlyName"="WinSrv"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{a68ca8b7-004f-d7b6-a698-07e2de0f1f5d}]
"ProviderFriendlyName"="Kernel General"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{a6ad76e3-867a-4635-91b3-4904ba6374d7}]
"ProviderFriendlyName"="ReadyBoost"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):40,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{aea1b4fa-97d1-45f2-a64c-4d69fffd92c9}]
"ProviderFriendlyName"="Group Policy"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{b2149bc3-9dfd-5866-92a7-b556b3a6aed0}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.DefaultLockApp"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{cfeb0608-330e-4410-b00d-56d8da9986e6}]
"ProviderFriendlyName"="AMP4"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{d781ca11-61c0-4387-b83d-af52d3d2dd6a}]
"ProviderFriendlyName"="Win32HeapRanges"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{d8975f88-7ddb-4ed0-91bf-3adf48c48e0c}]
"ProviderFriendlyName"="Microsoft-Windows-RPCSS"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000004
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{dbe9b383-7cf3-4331-91cc-a3cb16a3b538}]
"ProviderFriendlyName"="Winlogon"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{de7b24ea-73c8-4a09-985d-5bdadcfa9017}]
"ProviderFriendlyName"="TaskScheduler"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{e23b33b0-c8c9-472c-a5f9-f2bdfea0f156}]
"ProviderFriendlyName"="Software Licensing (V7)"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{e58f5f9c-3abb-5fc1-5ae5-dbe956bdbd33}]
"ProviderFriendlyName"="Microsoft.Windows.Shell.AboveLockShellComponent"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{e6307a09-292c-497e-aad6-498f68e2b619}]
"ProviderFriendlyName"="EMDMgmt"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{e8316a2d-0d94-4f52-85dd-1e15b66c5891}]
"ProviderFriendlyName"="CSRSS"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SYSTEM\ControlSet001\Control\WMI\Autologger\XBootMgrSession\{fb3cd94d-95ef-5a73-b35c-6c78451095ef}]
"ProviderFriendlyName"="Microsoft.Windows.CredProvDataModel"
"Enabled"=dword:00000001
"MatchAnyKeyword"=hex(b):00,00,00,00,00,00,00,00
"EnableLevel"=dword:00000000
"EnableProperty"=dword:00000100

[HKEY_LOCAL_MACHINE\PE_SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce]
"xBootMgrSleep"="\"C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit\\xbootmgrsleep.exe\" 10000 \"C:\\Program Files (x86)\\Windows Kits\\10\\Windows Performance Toolkit\\xbootmgr.exe\""

</pre>

Seal/promote the image.

On next boot you will have captured boot information:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1981-17" width="1140" height="938" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/WPT_In_Action-1.mp4?_=17" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/WPT_In_Action-1.mp4">http://theorypc.ca/wp-content/uploads/2017/01/WPT_In_Action-1.mp4</a></video>
</div>

To see how to use [Process MonitorÂ for boot tracing Citrix PVS Target Device's click here](https://theorypc.ca/2017/01/31/tracing-citrix-provisioning-service-target-device-boot-performance-process-monitor/).

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->