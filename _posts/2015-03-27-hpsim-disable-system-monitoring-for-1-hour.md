---
id: 566
title: HPSIM - Disable system monitoring for 1 hour
date: 2015-03-27T12:34:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/27/hpsim-disable-system-monitoring-for-1-hour/
permalink: /2015/03/27/hpsim-disable-system-monitoring-for-1-hour/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/hpsim-disable-system-monitoring-for-1.html
blogger_internal:
  - /feeds/7977773/posts/default/4202419575335591221
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - scripting
---
This script will disable monitoring in HPSIM for 1 hour.

<pre class="lang:ps decode:true ">#replace "-argumentlist $args[0]" with the system you want to disable monitoring
#replace WSHPSIM21 with your HPSIM server
#may need to replace path to mxnode.exe with whichever drive it's installed on for your system
 
Invoke-Command -ComputerName WSHPSIM21 -argumentlist $args[0] -ScriptBlock {
#select server
[xml]$serverXML = ."D:\Program Files\HP\Systems Insight Manager\bin\mxnode.exe" -lf $args[0]
 
#remove unneeded XML nodes...  With these nodes present, monitoring will not take effect.  They must be removed.
select-xml -Xml $serverXML -xpath "node-list/node/hw-attribute" | % {$serverXML."node-list".node.removechild($_.Node)}  | out-null
select-xml -Xml $serverXML -xpath "node-list/node/sw-attribute" | % {$serverXML."node-list".node.removechild($_.Node)}  | out-null
select-xml -Xml $serverXML -xpath "node-list/node/managementpath-list" | % {$serverXML."node-list".node.removechild($_.Node)}  | out-null
select-xml -Xml $serverXML -xpath "node-list/node/contract-warranty-data" | % {$serverXML."node-list".node.removechild($_.Node)}  | out-null
 
#set monitoring to suspended for 1 hr
$serverXML."node-list".node.SetAttribute("monitoring", "suspend.1h") | out-null
$serverXML.Save("$env:temp\server.xml") | out-null
 
#set monitoring.
."D:\Program Files\HP\Systems Insight Manager\bin\mxnode.exe" -m -f "$env:temp\server.xml" -w -v
}</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->