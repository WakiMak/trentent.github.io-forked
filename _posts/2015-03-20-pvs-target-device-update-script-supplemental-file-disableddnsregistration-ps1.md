---
id: 569
title: PVS Target Device Update Script - Supplemental File, disableddnsregistration.ps1
date: 2015-03-20T15:25:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/20/pvs-target-device-update-script-supplemental-file-disableddnsregistration-ps1/
permalink: /2015/03/20/pvs-target-device-update-script-supplemental-file-disableddnsregistration-ps1/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/pvs-target-device-update-script_83.html
blogger_internal:
  - /feeds/7977773/posts/default/4828617546590269495
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerShell
  - Provisioning Services
---
Our PVS servers are multi-homed with the Provisioning NIC on a seperate VLAN. Â Because of how our network is structured, our Provision NIC could register its DNS, but client computers would not be able to connect to it as it is a segregated network. Â This script sets the Provision NIC to NOT register to DNS.

<div>
  <pre class="lang:ps decode:true ">$provNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Provision"'
$prodNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Production"'
$adapters=Get-WmiObject Win32_NetworkAdapterConfiguration -filter 'IPEnabled=TRUE'
foreach($adapter in $adapters) {
  if ($prodNIC.name  -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($true,$false)
    $adapter.DomainDNSRegistrationEnabled
  }
  if ($provNIC.name -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($false,$false)
  }
}</pre>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->