---
id: 1784
title: AppV5 - Citrix User Profile Manager exclusions
date: 2016-10-21T13:25:39-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1784
permalink: /2016/10/21/appv5-citrix-user-profile-manager-exclusions/
categories:
  - Blog
tags:
  - AppV
  - Citrix
---
The Citrix User Profile Manager (UPM) needs a little configuration tweaking to work with AppV specifically it requires:

<div class="parbase base richtext section">
  <div class="show-bullets">
    <p class="p">
      <span style="color: #808080;"><a style="color: #808080;" href="http://docs.citrix.com/en-us/profile-management/5/upm-citrix-products-wrapper-den/upm-using-with-app-v.html">You can use Profile management 5.x in the same environment as Microsoft Application Virtualization 5.0 (App-V 5.0).</a></span>
    </p>
  </div>
</div>

<div class="parbase base richtext section">
  <div>
    <span style="color: #808080;"> You must exclude the following item using Profile management exclusions:</span>
  </div>
</div>

<div class="parbase base richtext section">
  <div>
    <span style="color: #808080;"> Profile Management\File system\Exclusion list\directories:</span>
  </div>
  
  <div class="show-bullets">
    <ul>
      <li>
        <span style="color: #808080;">AppData\Local\Microsoft\AppV</span>
      </li>
    </ul>
  </div>
</div>

<div class="parbase base richtext section">
  <div>
    <span style="color: #808080;"> If you don't exclude these items, App-V applications work the first time users access them but they fail, with an error, on subsequent logons.</span>
  </div>
</div>

But what happens when you \*don't\* exclude this directory?

We upgraded our Citrix UPM to 5.4.1 and in that process we moved from setting our inclusions/exclusions via the ini file to using Group Policy.  The original thought was simply adding the exclusions would add them to the existing list of default inclusions/exclusions which already has this directory set.  This line of thinking was incorrect.  Citrix's documentation states:

[<span class="importanttitle">Important:</span> If you use Group Policy rather than the .ini file (or you are rolling out a Group Policy deployment after a successful test with the .ini file), note that, unlike the installed .ini file, no items are included or excluded by default in the .adm or .admx file. This means you must add the default items manually to the file.](http://docs.citrix.com/en-us/profile-management/5/upm-tuning-den/upm-include-exclude-defaults-den.html)

When we enabled Group Policy for the exclusions and set the path (for something unrelated to AppV) then it was the ONLY item being excluded from AppV and we were having the issue described by Citrix.  Our application would launch the first time, or oddly, just for the user on that specific server.  When they launched it again on another server it would fail until their user profile was deleted from the profile share.

I [setup AppV5 debug logging](https://theorypc.ca/2016/03/24/appv5-the-trouble-with-appv5-logs-and-a-solution/) and traced a launch of what this failure looked like when our user tried to start an AppV application:

```shell
[4]641C.459C::2016-10-07 10:38:29.587 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.588 - VObjects: [25628].[17820]: INFO: Function hook called: CreateEventW  [object_hooks::CreateEventW; 362] 
[4]641C.459C::2016-10-07 10:38:29.587 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.588 - VObjects: [25628].[17820]: INFO: Function hook called: CreateEventExW  [object_hooks::CreateEventExW; 287] 
[6]1780.11DC::2016-10-07 10:38:29.625 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.626 - PackageConfig: [6016].[4572]: INFO: AddUserPackage called for package: 8bf3c222-0a7c-4c88-99d0-11ce6702a9e1, version: dd00fc42-c70a-426e-b00e-70c6db825adc, group: 00000000-0000-0000-0000-000000000000, version 00000000-0000-0000-0000-000000000000, and user: S-1-5-21-38857442-2693285798-3636612711-15166942  [AppV::Client::Virtualization::PackageConfigManagerImpl::add_user_package_config; 386] 
[6]1780.11DC::2016-10-07 10:38:29.625 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.626 - PackageConfig: [6016].[4572]: DEBUG: AddUserPackage derived values: package moniker '8BF3C222-0A7C-4C88-99D0-11CE6702A9E1_DD00FC42-C70A-426E-B00E-70C6DB825ADC', group moniker ''  [AppV::Client::Virtualization::PackageConfigManagerImpl::add_user_package_config; 406] 
[2]1780.11DC::2016-10-07 10:38:29.649 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.651 - PackageConfig: [6016].[4572]: DEBUG: Calculated COW mapping: from 'D:\AppVData\PackageInstallationRoot\8BF3C222-0A7C-4C88-99D0-11CE6702A9E1\DD00FC42-C70A-426E-B00E-70C6DB825ADC\root\VFS\AppData' to 'C:\Users\adtest93\AppData\Roaming\Microsoft\AppV\Client\VFS\8BF3C222-0A7C-4C88-99D0-11CE6702A9E1\AppData'  [AppV::Client::Virtualization::PackageConfigManagerImpl::get_vfsc_mappings; 1361] 
[2]1780.11DC::2016-10-07 10:38:29.650 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.651 - PackageConfig: [6016].[4572]: DEBUG: Calculated COW mapping: from 'D:\AppVData\PackageInstallationRoot\8BF3C222-0A7C-4C88-99D0-11CE6702A9E1\DD00FC42-C70A-426E-B00E-70C6DB825ADC\root\VFS\AppVPackageDrive' to 'C:\Users\adtest93\AppData\Local\Microsoft\AppV\Client\VFS\8BF3C222-0A7C-4C88-99D0-11CE6702A9E1\AppVPackageDrive'  [AppV::Client::Virtualization::PackageConfigManagerImpl::get_vfsc_mappings; 1361] 
[2]1780.11DC::2016-10-07 10:38:29.652 [Microsoft-AppV-Client]User VFS COW folder validation failed for source: C:\Program Files, target: C:\Users\adtest93\AppData\Local\Microsoft\AppV\Client\VFS\8BF3C222-0A7C-4C88-99D0-11CE6702A9E1\AppVPackageDriveS. 
[2]1780.11DC::2016-10-07 10:38:29.652 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.652 - VirtualizationManager: [6016].[4572]: ERROR: 64 notification failed with error 5746831735727849499. Package 8bf3c222-0a7c-4c88-99d0-11ce6702a9e1, version dd00fc42-c70a-426e-b00e-70c6db825adc, pid 24604, ve id 0.  [AppV::Client::Virtualization::NotificationListenerImpl::ProcessRequest; 256] 
[5]465C.8234::2016-10-07 10:38:29.652 [Microsoft-AppV-ServiceLog]2016-Oct-07 10:38:29.652 - VEMgrDrv: [18012].[33332]: ERROR: ConfigurationManager::IsPublishedForUser_Internal() - Request to generate mappings for globally-published package failed. package moniker 8BF3C222-0A7C-4C88-99D0-11CE6702A9E1_DD00FC42-C70A-426E-B00E-70C6DB825ADC. group moniker . user sid S-1-5-21-38857442-2693285798-3636612711-15166942. result -1073741823.
```

The lesson?

If you are using a Profile Manager ensure your exclusions for AppV are applied correctly!  If you miss you may run into this weird behaviour.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
