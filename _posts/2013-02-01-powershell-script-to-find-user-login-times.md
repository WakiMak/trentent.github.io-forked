---
id: 662
title: Powershell script to find user login times
date: 2013-02-01T01:07:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/02/01/powershell-script-to-find-user-login-times/
permalink: /2013/02/01/powershell-script-to-find-user-login-times/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/01/powershell-script-to-find-user-login.html
blogger_internal:
  - /feeds/7977773/posts/default/4056207178956128270
categories:
  - Blog
  - Uncategorized
tags:
  - Group Policy
  - PowerShell
  - scripting
---
With Windows Server 2008 and on the event log &#8220;Group Policy&#8221; will track how long it takes a user to login. I&#8217;ve created a script that will pull all this information into a file.

<pre class="lang:default decode:true ">=================List-of-comps.txt======================
Server1
Server2
Server3
Server4</pre>

&nbsp;

<pre class="lang:ps decode:true ">$comp = import-csv "C:\list-of-comps.txt"
foreach ($entry in $comp) {(get-winevent -listprovider microsoft-windows-grouppolicy) |get-winevent -computername $entry.comp |where {$_.id -eq 8001}| format-table timecreated, message -auto | out-file -append "C:\Users\trententtye\Desktop\New Text Document (2).txt"}</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->