---
id: 628
title: Query a bunch of Windows 2003 event logs
date: 2013-08-20T16:29:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/08/20/query-a-bunch-of-windows-2003-event-logs/
permalink: /2013/08/20/query-a-bunch-of-windows-2003-event-logs/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/08/query-bunch-of-windows-2003-event-logs.html
blogger_internal:
  - /feeds/7977773/posts/default/6535076383416495964
categories:
  - Blog
  - Uncategorized
tags:
  - "2003"
  - scripting
---
<pre class="lang:batch decode:true ">for /f %A IN ('type systems.txt') DO (
cscript.exe C:\windows\system32\EVENTQUERY.vbs /S %A /FI "ID eq 3001" /L Application &gt;&gt; list.txt
)</pre>

This will find event ID 3001 in the Application log file with a list of computers from &#8220;systems.txt&#8221;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->