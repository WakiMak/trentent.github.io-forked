---
id: 638
title: 'Powershell script to manipulate &#8220;Register this connection&#8217;s addresses in DNS&#8221;'
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
  - XenApp
---
We run a multihome NIC setup with our Citrix PVS servers and the &#8220;Provisioning&#8221; Network is a seperate VLAN that is only used by the PVS servers and goes no where. Â Unfortunately, however, the &#8220;Provision&#8221; NIC can register itself in the DNS, causing devices outside of the Provisioning network (everyone) to resolve to the incorrect address. Â To resolve this I ran this script across all my vDisks to remove the ability of the provision network to register itself as available in DNS.:

<pre class="lang:ps decode:true ">$provNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Provision"'
$prodNic=Get-WmiObject Win32_NetworkAdapter -filter 'netconnectionid ="Production"'
$adapters=Get-WmiObject Win32_NetworkAdapterConfiguration -filter 'IPEnabled=TRUE'
foreach($adapter in $adapters) {
  if ($prodNIC.name -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($true,$false)
  }
  if ($provNIC.name -eq $adapter.Description) {
    $adapter.SetDynamicDNSRegistration($false,$false)
  }
}</pre>

As you can see above, we have two NICs, &#8220;Provision&#8221; and &#8220;Production&#8221;. Â We do not want &#8220;Provision&#8221; registerted so it gets the $false,$false set, whereas &#8220;Production&#8221; gets $true,$false.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->