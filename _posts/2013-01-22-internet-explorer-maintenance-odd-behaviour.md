---
id: 664
title: Internet Explorer Maintenance Odd Behaviour
date: 2013-01-22T13:09:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/01/22/internet-explorer-maintenance-odd-behaviour/
permalink: /2013/01/22/internet-explorer-maintenance-odd-behaviour/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/01/internet-explorer-maintenance-odd.html
blogger_internal:
  - /feeds/7977773/posts/default/846259376430160665
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - Internet Explorer
---
Recently, I was experiencing some odd behaviour on some of our Windows boxes.Â We were getting redirected to a proxy server but RSOP.MSC and group policy showed that we should have been good in the GUI.

The issue is IE Maintenance will store it's settings in a INS file as opposed to the registry and for some reason IE Maintenance in GPEDIT.MSC doesn't display the values.Â This document details it more:

<http://blogs.technet.com/b/perfguru/archive/2008/04/26/how-to-troubleshoot-internet-explorer-s-maintenance-group-policy.aspx>

The INS files created via GPO are placed in the following locations:  
2003-"C:\Documents and Settings\trententtye\Local Settings\Application Data\Microsoft\Internet Explorer"  
2008-"C:\Users\trententtye\AppData\Local\Microsoft\Internet Explorer"

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->