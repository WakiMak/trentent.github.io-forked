---
id: 612
title: Citrix, Internet Explorer and AppV
date: 2014-04-24T15:50:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/04/24/citrix-internet-explorer-and-appv/
permalink: /2014/04/24/citrix-internet-explorer-and-appv/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/04/citrix-internet-explorer-and-appv.html
blogger_internal:
  - /feeds/7977773/posts/default/6223752222393847801
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Citrix
  - &
---
I wrote about the issue of [Internet Explorer and AppV here](http://trentent.blogspot.ca/2013/01/internet-explorer-popping-up-outside.html) before. Â Long story short, Citrix changes the iexplorer.exe launch handle to point to its own stub of iexplore.exe. Â I suggested changing the iexplore in the registry to point to the proper version of IE, but the Citrix version appears to be a FTA (File Type Association) stub of some sort. Â Another solution would be to use the full path to iexplorer.exe (C:\Program Files (x86)\Internet Explorer\iexplore.exe) in the path of the published Citrix application. Â This avoids the stub being launched.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->