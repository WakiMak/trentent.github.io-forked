---
id: 1007
title: 'HPSIM &#8211; Disable system monitoring for 1 hour'
date: 2016-04-24T18:43:09-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/566-revision-v1/
permalink: /2016/04/24/566-revision-v1/
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