---
id: 597
title: 'AppV 5 - Formatted volume allocation size matters'
date: 2014-08-15T13:20:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/15/appv-5-formatted-volume-allocation-size-matters/
permalink: /2014/08/15/appv-5-formatted-volume-allocation-size-matters/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/appv-5-formatted-volume-allocation-size.html
blogger_internal:
  - /feeds/7977773/posts/default/5952840923905635961
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Performance
---
After discovering that AppV 5 configures an allocation size independent of the file system beneath it, I explored the impact of different formatted allocation sizes on AppV packages; specifically mounting AppV packages.

I took our AppV setup and set the PackageInstallationRoot to D:AppVDataPackageInstallationRoot and then formatted the D: to different allocation sizes and then mounted the AppV package. Â I timed how long it took to mount the package over 4 runs per allocation choice, took the AppV file size allocation, and the total, actual size of the package on the drive. Â Package Details:

<pre class="lang:default decode:true ">Package size: 1838658907 (1.71GB)
Files: 9106
Folders: 357</pre>

The results:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto; text-align: center;" href="http://1.bp.blogspot.com/-hnk9_X6PvlQ/U-5a86qbJ_I/AAAAAAAAAfw/YXoro_rkRN0/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B1.07.37%2BPM.png"><img src="http://1.bp.blogspot.com/-hnk9_X6PvlQ/U-5a86qbJ_I/AAAAAAAAAfw/YXoro_rkRN0/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B1.07.37%2BPM.png" width="180" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Different Allocation Sizes
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-DfA0eXq8Zdo/U-5a25U8oBI/AAAAAAAAAfY/EoRKdyzVhio/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.50.58%2BPM.png"><img src="http://3.bp.blogspot.com/-DfA0eXq8Zdo/U-5a25U8oBI/AAAAAAAAAfY/EoRKdyzVhio/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.50.58%2BPM.png" width="320" height="277" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Actual Used Space vs. Allocation size
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-r5tgJPpbhOg/U-5a5QZ-BiI/AAAAAAAAAfk/bOJBSRGzcMk/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.53.00%2BPM.png"><img src="http://2.bp.blogspot.com/-r5tgJPpbhOg/U-5a5QZ-BiI/AAAAAAAAAfk/bOJBSRGzcMk/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.53.00%2BPM.png" width="320" height="292" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Actual AppV 5 file allocation size vs. formatted allocation size
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-MEFxng35c5w/U-5a5U6mPbI/AAAAAAAAAfg/Rn9vqd72B9w/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.55.09%2BPM.png"><img src="http://1.bp.blogspot.com/-MEFxng35c5w/U-5a5U6mPbI/AAAAAAAAAfg/Rn9vqd72B9w/s1600/Screen%2BShot%2B2014-08-15%2Bat%2B12.55.09%2BPM.png" width="320" height="290" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      AppV 5 Mount Time vs. formatted allocation size
    </td>
  </tr>
</table>

I would propose using 64KB allocation sizes for the AppV volume if possible (note, this only applies to fully mounted packages). Â There does appear to be some benefit to using larger allocation sizes, one of the main ones is the properties of the PackageInstallationRoot now reflects more accurately the actual consumed filesystem on Windows 2008 / Windows 7. Â Another is there is a speed improvement around the 4KB allocation size then minor increases afterwards.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->