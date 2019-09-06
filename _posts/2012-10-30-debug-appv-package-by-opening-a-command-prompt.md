---
id: 671
title: Debug AppV package by opening a command prompt
date: 2012-10-30T14:25:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/10/30/debug-appv-package-by-opening-a-command-prompt/
permalink: /2012/10/30/debug-appv-package-by-opening-a-command-prompt/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/10/debug-appv-package-by-opening-command.html
blogger_internal:
  - /feeds/7977773/posts/default/374938412863740824
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
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