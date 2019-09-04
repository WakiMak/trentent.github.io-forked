---
id: 598
title: 'AppV 5 - changing PackageInstallationRoot impacts AppVPackageDrive variable'
date: 2014-08-14T15:02:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/14/appv-5-changing-packageinstallationroot-impacts-appvpackagedrive-variable/
permalink: /2014/08/14/appv-5-changing-packageinstallationroot-impacts-appvpackagedrive-variable/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/appv-5-changing-packageinstallationroot.html
blogger_internal:
  - /feeds/7977773/posts/default/7429508834340703813
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
I just sequenced an application (Epic) and it puts two folders on the root of the C drive. Â C:\Epic and C:\Crashdumps. Â When I published the application to our Citrix servers the application failed to launch. Â Procmon'ing it I saw that Epic was unable to find C:\Epic. Â I opened a command prompt and tried to cd /d C:\Epic which failed. Â On a hunch I tried cd /d D:\Epic and lo and behold it was successful and I was put in that folder. Â I investigated this and created a package with two folders on the root of the C:\. Â They were C:\test and C:\test\_happy\_folder. Â When I published the package on our Citrix servers I could not cd into them on the C:\ drive but I could because they were on the D:\ drive. Â Examining the package it showed:

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-J4-3LlsfsdU/U-0hIm7elnI/AAAAAAAAAe4/f7G5R3fnrE0/s1600/test-happy-folder.png"><img src="http://3.bp.blogspot.com/-J4-3LlsfsdU/U-0hIm7elnI/AAAAAAAAAe4/f7G5R3fnrE0/s1600/test-happy-folder.png" width="320" height="83" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

Why was it going to D:? Â We have our servers setup so that the PackageInstallationRoot registry key points to the D:.

<div style="clear: both; text-align: center;">
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-8qAAQdAPIDg/U-0hvLqdsPI/AAAAAAAAAfI/0x7qp1e-7YY/s1600/packageinstallationroot.png"><img src="http://3.bp.blogspot.com/-8qAAQdAPIDg/U-0hvLqdsPI/AAAAAAAAAfI/0x7qp1e-7YY/s1600/packageinstallationroot.png" width="320" height="136" border="0" /></a>
</div>

When I changed PackageInstallationRoot to be C:\AppVData\PackageInstallationRoot I could then cd /d C:\Epic without issue. Â This is unfortunate but I do not think this was the expected behaviour because we sequence to C:. Â To fix this I set the PVAD to C:\Crashdumps (which does expand out to the C: when specified in the sequencer, not the D:) and moved the C:\Epic folder to within a tokenized folder (programfiles\Epic) which correctly expands out to C:\Program Files\Epic. Â I then needed to hunt down all config files and scripts that point to C:\Epic and update them to point to C:\Program Files\Epic. Â Unfortunately, I do not know of a way to modify AppVPackageDrive to a drive letter without changing PackageInstallationRoot because I would love for my sequenced applications to stick to C:\ so I don't need to do any workarounds.

Essentially, I guess what I'm saying is I hope AppV 5, sometime in the future, allows you to either hard code the AppVPackageDrive variable to a specific drive letter, or use some other tokenized variable that points to a specific drive letter ({CDrive},{DDrive},{EDrive},{FDrive}, etc.)

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->