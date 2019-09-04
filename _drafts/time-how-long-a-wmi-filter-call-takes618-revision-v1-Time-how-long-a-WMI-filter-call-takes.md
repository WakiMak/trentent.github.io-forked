---
id: 1074
title: Time how long a WMI filter call takes
date: 2016-04-24T22:58:25-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/618-revision-v1/
permalink: /2016/04/24/618-revision-v1/
---
The following command will time how long a WMI filter call will take to execute on your server/PC.

<pre class="lang:ps decode:true ">$query = "Select * from Win32_Processor where AddressWidth = '32'"
Measure-Command { Get-WmiObject -Query $query }</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->