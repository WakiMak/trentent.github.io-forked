---
id: 1194
title: 'ADFind one-liner -> Find operating system of computer in AD'
date: 2016-04-25T11:44:01-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/704-revision-v1/
permalink: /2016/04/25/704-revision-v1/
---
Nice 🙂

> <pre class="lang:default decode:true ">adfind -b "OU=Domain Controllers,DC=lab,DC=com" -f "&(objectcategory=computer)" operatingSystem -csv</pre>
> 
> &nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->