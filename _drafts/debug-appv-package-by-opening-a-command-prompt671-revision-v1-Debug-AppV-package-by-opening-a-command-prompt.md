---
id: 1152
title: Debug AppV package by opening a command prompt
date: 2016-04-25T08:59:27-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/671-revision-v1/
permalink: /2016/04/25/671-revision-v1/
---
Open a command prompt and then run the following:

<pre class="lang:batch decode:true ">SFTMIME QUERY OBJ:APP /SHORT
IT999 2.3
TTEditor 7.1
Paris 3.7

SFTTRAY /EXE cmd.exe /LAUNCH "Paris 3.7"</pre>

This will launch the cmd.exe in the package.

Nice.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->