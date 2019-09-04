---
id: 1089
title: Query a bunch of Windows 2003 event logs
date: 2016-04-25T07:44:56-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/628-revision-v1/
permalink: /2016/04/25/628-revision-v1/
---
<pre class="lang:batch decode:true ">for /f %A IN ('type systems.txt') DO (
cscript.exe C:\windows\system32\EVENTQUERY.vbs /S %A /FI "ID eq 3001" /L Application &gt;&gt; list.txt
)</pre>

This will find event ID 3001 in the Application log file with a list of computers from &#8220;systems.txt&#8221;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->