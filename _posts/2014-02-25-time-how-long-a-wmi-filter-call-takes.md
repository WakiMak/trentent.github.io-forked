---
id: 618
title: Time how long a WMI filter call takes
date: 2014-02-25T11:07:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/02/25/time-how-long-a-wmi-filter-call-takes/
permalink: /2014/02/25/time-how-long-a-wmi-filter-call-takes/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/02/time-how-long-wmi-filter-call-takes.html
blogger_internal:
  - /feeds/7977773/posts/default/2848971325155428034
categories:
  - Blog
  - Uncategorized
tags:
  - Performance
  - PowerShell
  - scripting
---
The following command will time how long a WMI filter call will take to execute on your server/PC.


```powershell
$query = "Select * from Win32_Processor where AddressWidth = '32'"
Measure-Command { Get-WmiObject -Query $query 
```


&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->