---
id: 1145
title: Internet Explorer Maintenance Odd Behaviour
date: 2016-04-25T08:51:24-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/664-revision-v1/
permalink: /2016/04/25/664-revision-v1/
---
Recently, I was experiencing some odd behaviour on some of our Windows boxes.Â We were getting redirected to a proxy server but RSOP.MSC and group policy showed that we should have been good in the GUI.

The issue is IE Maintenance will store it&#8217;s settings in a INS file as opposed to the registry and for some reason IE Maintenance in GPEDIT.MSC doesn&#8217;t display the values.Â This document details it more:

<http://blogs.technet.com/b/perfguru/archive/2008/04/26/how-to-troubleshoot-internet-explorer-s-maintenance-group-policy.aspx>

The INS files created via GPO are placed in the following locations:  
2003-&#8220;C:\Documents and Settings\trententtye\Local Settings\Application Data\Microsoft\Internet Explorer&#8221;  
2008-&#8220;C:\Users\trententtye\AppData\Local\Microsoft\Internet Explorer&#8221;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->