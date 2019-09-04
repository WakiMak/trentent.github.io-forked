---
id: 1067
title: Citrix, Internet Explorer and AppV
date: 2016-04-24T22:48:01-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/612-revision-v1/
permalink: /2016/04/24/612-revision-v1/
---
I wrote about the issue of [Internet Explorer and AppV here](http://trentent.blogspot.ca/2013/01/internet-explorer-popping-up-outside.html) before. Â Long story short, Citrix changes the iexplorer.exe launch handle to point to its own stub of iexplore.exe. Â I suggested changing the iexplore in the registry to point to the proper version of IE, but the Citrix version appears to be a FTA (File Type Association) stub of some sort. Â Another solution would be to use the full path to iexplorer.exe (C:\Program Files (x86)\Internet Explorer\iexplore.exe) in the path of the published Citrix application. Â This avoids the stub being launched.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->