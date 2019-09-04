---
id: 1820
title: 'AppV5 &#8211; 0xFD01F25-0x2 and deciphering some of these messages'
date: 2016-12-13T11:18:32-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1820
permalink: /2016/12/13/appv5-0xfd01f25-0x2-and-deciphering-some-of-these-messages/
image: /wp-content/uploads/2016/12/error.png
categories:
  - Blog
tags:
  - AppV
---
We recently had an issue with some users launching an AppV 5 application. � They were getting an error message:

<img class="aligncenter size-full wp-image-1823" src="http://theorypc.ca/wp-content/uploads/2016/12/event.png" alt="" width="632" height="435" srcset="http://theorypc.ca/wp-content/uploads/2016/12/event.png 632w, http://theorypc.ca/wp-content/uploads/2016/12/event-300x206.png 300w" sizes="(max-width: 632px) 100vw, 632px" /> 

<pre class="lang:default decode:true">Process 10372 failed to start due to Virtual Filesystem subsystem failure. Package ID {f20085a5-9efb-4460-a474-a9db9e3fada5}. Version ID {6bc31f1c-7abd-4c89-aadb-b0d9eb903f3d}. Error: 0xFD01F25-0x2</pre>

In order to troubleshoot this issue more effectively, I turned on &#8216;[Event Tracing for Windows](https://theorypc.ca/2016/03/24/appv5-the-trouble-with-appv5-logs-and-a-solution/)&#8216; for the AppV logs and captured the output. � Searching for the error code revealed the following:

<pre class="lang:default decode:true">[0]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.630 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - VReg: [3296].[8804]: INFO: Incoming runtime settings are valid.  [vreg_runtime_configurator::initialize; 84] 
[0]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.630 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - VReg: [3296].[8804]: INFO: Configuration initialization complete.  Mode: 1 Local COW creation path:Software\Classes\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5 Roaming COW creation path:Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5 Secure COW creation path:Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\S-1-5-21-38857442-2693285798-3636612711-15166942 Client package registry root:\REGISTRY\MACHINE\Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\Versions\6BC31F1C-7ABD-4C89-AADB-B0D9EB903F3D Client local COW root: \REGISTRY\USER\S-1-5-21-38857442-2693285798-3636612711-15166942_Classes\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5 Client roaming COW root: \REGISTRY\USER\S-1-5-21-38857442-2693285798-3636612711-15166942\Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5 Client secure COW root: \REGISTRY\MACHINE\Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\S-1-5-21-38857442-2693285798-3636612711-15166942  [vreg_runtime_configurator::initialize; 111] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.631 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - VReg: [3296].[8804]: INFO: Successfully validated user COW key at \REGISTRY\USER\S-1-5-21-38857442-2693285798-3636612711-15166942_Classes\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5.  [vreg_runtime::ensure_cows; 203] 
[2]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.633 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - VReg: [3296].[8804]: INFO: Successfully validated user COW key at \REGISTRY\USER\S-1-5-21-38857442-2693285798-3636612711-15166942\Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5.  [vreg_runtime::ensure_cows; 216] 
[2]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.634 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - VReg: [3296].[8804]: INFO: Successfully validated user COW key at Software\Microsoft\AppV\Client\Packages\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\S-1-5-21-38857442-2693285798-3636612711-15166942.  [vreg_runtime::ensure_cows; 229] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.636 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.633 - SubsystemController: [3296].[8804]: INFO: Virtual Registry start_runtime() success. Package {F20085A5-9EFB-4460-A474-A9DB9E3FADA5} - {6BC31F1C-7ABD-4C89-AADB-B0D9EB903F3D} user S-1-5-21-38857442-2693285798-3636612711-15166942  [AppV::Client::Virtualization::VirtualEnvironment::start_runtimes; 129] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - Vfs: [3296].[8804]: ERROR: Failed to get long path from '\\?\C:\Users\adtest93\AppData\Roaming\Microsoft\Windows\Templates'.  Error code: 0x0FD01F25-00000002  [vfs_path_helper::get_path_long_name; 141] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - Vfs: [3296].[8804]: ERROR: Failed to load runtime configurations. Error code: 0x0FD01F25-00000002  [vfs_runtime::start_runtime_internal; 103] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - SubsystemController: [3296].[8804]: ERROR: start_runtime() failed on Virtual Filesystem subsystem. Error 1139444949498986498.  [AppV::Client::Virtualization::VirtualEnvironment::start_runtimes; 105] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-Client]Process 18552 failed to start due to Virtual Filesystem subsystem failure. Package ID {f20085a5-9efb-4460-a474-a9db9e3fada5}. Version ID {6bc31f1c-7abd-4c89-aadb-b0d9eb903f3d}. Error: 0xFD01F25-0x2 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - SubsystemController: [3296].[8804]: INFO: Removing VE from map. package: {F20085A5-9EFB-4460-A474-A9DB9E3FADA5} - {6BC31F1C-7ABD-4C89-AADB-B0D9EB903F3D}, user SID: S-1-5-21-38857442-2693285798-3636612711-15166942, VE id: 87  [AppV::Client::Virtualization::VirtualEnvironmentMap::remove_ve; 106] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - SubsystemController: [3296].[8804]: INFO: VECreated activity received. Package GUID f20085a5-9efb-4460-a474-a9db9e3fada5, version GUID 6bc31f1c-7abd-4c89-aadb-b0d9eb903f3d, user S-1-5-21-38857442-2693285798-3636612711-15166942, result 1139444949498986498.  [AppV::Client::Virtualization::SubsystemController::HandleActivity_VirtualEnvironmentCreated; 529] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.673 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - Orchestrator: [3296].[8804]: ERROR: Activity VirtualEnvironmentCreated #2656 failed on component SubsystemController (error: 0x0FD01F25-00000002).  [AppV::Client::Orchestration::ClientActivity::ExecuteActivity; 103] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.675 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - Orchestrator: [3296].[8804]: INFO: Activity VirtualEnvironmentCreated #2656 for (entity f20085a5-9efb-4460-a474-a9db9e3fada5, version 6bc31f1c-7abd-4c89-aadb-b0d9eb903f3d) is no longer coordinated with other running activity instances.  [AppV::Client::Orchestration::RunningActivities::Remove; 234] 
[3]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.675 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.664 - Orchestrator: [3296].[8804]: ERROR: VirtualEnvironmentCreated activity #2656 failed with error code 0x55900D02-00000501.  [AppV::Client::Orchestration::ActivityManagerImpl&lt;struct `public: static void __cdecl AppV::Client::Orchestration::ActivityManagerFactory::CreateInstance(class std::shared_ptr&lt;class AppV::Client::Orchestration::ActivityManager&gt; &,enum AppV::Shared::RuntimeNamespaceConfiguration)'::`2'::Empty&gt;::RequestActivity; 304] 
[5]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.675 [Microsoft-AppV-ServiceLog]2016-Dec-13 10:25:56.680 - VirtualizationManager: [3296].[8804]: ERROR: 7 component failed to handle 12 activity. Error 1139444949498986498.  [AppV::Client::Virtualization::VirtualizationManager::HandleRequestActivityFailure; 646] 
[5]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.675 [Microsoft-AppV-Client]D:\AppVData\PackageInstallationRoot\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\6BC31F1C-7ABD-4C89-AADB-B0D9EB903F3D\Root\VFS\ProgramFilesX86\Microsoft Office\Office\MSACCESS.EXE application from package ID {f20085a5-9efb-4460-a474-a9db9e3fada5} failed to launch. process ID 18552, error 0xFD01F25-0x2. 
[5]0CE0.2264::‎2016‎-‎12‎-‎13 10:25:56.675 [Microsoft-AppV-Client]D:\AppVData\PackageInstallationRoot\F20085A5-9EFB-4460-A474-A9DB9E3FADA5\6BC31F1C-7ABD-4C89-AADB-B0D9EB903F3D\Root\VFS\ProgramFilesX86\Microsoft Office\Office\MSACCESS.EXE application from package ID {f20085a5-9efb-4460-a474-a9db9e3fada5} failed to launch. process ID 18552, error 0x55900D02-0x501. 
</pre>

&nbsp;

The first line that shows an &#8216;error&#8217;:

<pre class="lang:default decode:true">ERROR: Failed to get long path from '\\?\C:\Users\adtest93\AppData\Roaming\Microsoft\Windows\Templates'</pre>

I navigated to that path, and sure enough, a &#8216;Templates&#8217; folder was not present.

I did a procmon trace during that user logon and noticed the folder was never created. � AppV, for some reason, when it did not find this folder threw up an error. � If I create that folder I was able to launch the application without issue.

So what does the error message &#8220;0xFD01F25-0x2&#8221; mean? � Well, the first portion split by the &#8216;dash&#8217; is the component that is explained to decipher where in AppV this issue is occuring. � The second string (0x2) is more interesting because it actually tells us something. � Microsoft has these [short codes documented here](https://msdn.microsoft.com/en-us/library/windows/desktop/ms681382(v=vs.85).aspx).

<pre class="lang:default decode:true">ERROR_SUCCESS0 (0x0)
The operation completed successfully.
ERROR_INVALID_FUNCTION1 (0x1)
Incorrect function.
<strong>ERROR_FILE_NOT_FOUND2 (0x2)</strong>
The system cannot find the file specified.
ERROR_PATH_NOT_FOUND3 (0x3)
The system cannot find the path specified.
ERROR_TOO_MANY_OPEN_FILES4 (0x4)
The system cannot open the file.
ERROR_ACCESS_DENIED5 (0x5)
Access is denied.
</pre>

0x2 = the system cannot find the file specified. � It&#8217;s actually looking for a folder, but the object didn&#8217;t exist and that&#8217;s the code it generated. � So if you see that second octect in an AppV error, the short system error code may give you a more precise clue to what is occuring and how you can fix it.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->