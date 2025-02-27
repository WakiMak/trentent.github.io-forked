---
id: 638
title: Powershell script to manipulate "Register this connection's addresses in DNS"
date: 2013-07-08T12:57:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/07/08/powershell-script-to-manipulate-register-this-connections-addresses-in-dns/
permalink: /2013/07/08/powershell-script-to-manipulate-register-this-connections-addresses-in-dns/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/07/powershell-script-to-manipulate.html
blogger_internal:
  - /feeds/7977773/posts/default/3184542254377123448
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - Citrix
  - PowerShell
  - Provisioning Services
  - scripting

---
We run a multihome NIC setup with our Citrix PVS servers and the "Provisioning" Network is a seperate VLAN that is only used by the PVS servers and goes no where.  Unfortunately, however, the "Provision" NIC can register itself in the DNS, causing devices outside of the Provisioning network (everyone) to resolve to the incorrect address.  To resolve this I ran this script across all my vDisks to remove the ability of the provision network to register itself as available in DNS.:


```powershell
$provNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Provision"'
$prodNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Production"'
$adapters=Get-WmiObject Win32_NetworkAdapterConfiguration -filter 'IPEnabled=TRUE'
foreach($adapter in $adapters) {
  if ($prodNIC.name -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($true,$false)
  }
  if ($provNIC.name -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($false,$false)
  }

```


As you can see above, we have two NICs, "Provision" and "Production".  We do not want "Provision" registerted so it gets the $false,$false set, whereas "Production" gets $true,$false.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->