---
id: 624
title: PowerCLI Fix VMWare Time Sync issue
date: 2013-10-15T11:15:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/10/15/powercli-fix-vmware-time-sync-issue/
permalink: /2013/10/15/powercli-fix-vmware-time-sync-issue/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/10/powercli-fix-vmware-time-sync-issue.html
blogger_internal:
  - /feeds/7977773/posts/default/3700839982632311069
categories:
  - Blog
  - Uncategorized
tags:
  - PowerCLI
  - PowerShell
  - VMWare
  - VMWare tools
---

```powershell
# ===========================================================================================================
#
# Created by:  Trentent Tye
#
# Creation Date: Oct 15, 2013
#
# File Name:  Fix-Time-Sync.ps1
#
# Description:  This script will be used to resolve an issue with VMWare where the VMWare Tools cause a
#                   time sync with the host.  If the host has an incorrect time it will knock the time out of
#                   sync on the guest.  To resolve this issue some text entires need to be made to the VMX
#                   fix.  This is detailed here:
#                   http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1189
#
# ===========================================================================================================

Add-PSSnapin VMware.VimAutomation.Core
connect-viserver wsvcenter20

$ExtraOptions = @{
    "tools.syncTime"="0";
    "time.synchronize.continue"="0";
    "time.synchronize.restore"="0";
    "time.synchronize.resume.disk"="0";
    "time.synchronize.shrink"="0";
    "time.synchronize.tools.startup"="0";
    "time.synchronize.tools.enable" = "0";
    "time.synchronize.resume.host" = "0";
}   # build our configspec using the hashtable from above.  I prefer this
# method over the use of files b/c it has one less needless dependency.
$vmConfigSpec = New-Object VMware.Vim.VirtualMachineConfigSpec
# note we have to call the GetEnumerator before we can iterate through
Foreach ($Option in $ExtraOptions.GetEnumerator()) {
    $OptionValue = New-Object VMware.Vim.optionvalue
    $OptionValue.Key = $Option.Key
    $OptionValue.Value = $Option.Value
    $vmConfigSpec.extraconfig += $OptionValue
}
# Get all vm's starting with name:
$VMView = Get-View -ViewType VirtualMachine -Filter @{"Name" = "WSCTX"}

foreach($vm in $VMView){
    $vm.ReconfigVM_Task($vmConfigSpec)
}

# Get all vm's starting with name:
$VMView = Get-View -ViewType VirtualMachine -Filter @{"Name" = "WSAPV"}

foreach($vm in $VMView){
    $vm.ReconfigVM_Task($vmConfigSpec)

```


&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->