---
id: 1168
title: Retrieving Citrix user accounts via PowerShell
date: 2016-04-25T11:03:45-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/676-revision-v1/
permalink: /2016/04/25/676-revision-v1/
---
Retrieving Citrix user accounts via PowerShell

Here&#8217;s a neat little two liner to pull all the AD accounts associated with Citrix applications:

<pre class="lang:ps decode:true ">$accounts = Get-XAApplicationReport * | select-object Accounts

$accounts | foreach {$_.accounts | select-object AccountDisplayName} | export-csv "%userprofile%desktopapp.csv" -noclobber</pre>

Awesome.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->