---
id: 2087
title: 'AppV &#8211; Profiling Application Launch Performance'
date: 2017-03-30T09:16:07-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2087
permalink: /?p=2087
categories:
  - Blog
---
We started receiving reports that an application launch was starting to take slightly longer than normal. Â We have multiple different versions of this application in AppV with each version differing by a patch level applied. Â This application is very registry heavy (approximately a 80MB registry DAT in the AppV package) and registry is a bit of an achilles heel for AppV. Â When AppV has an package launch for the first time it will load the hive from the package and import it into the HKLM\Software hive. Â An 80MB DAT file takes a long time to load, no matter what. Â I&#8217;ve found that loading each of these packages takes longer and longer each time. Â The first time it loads the package takes around 600 seconds, the second time 800-900 seconds, and a third time takes 2400 seconds.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->