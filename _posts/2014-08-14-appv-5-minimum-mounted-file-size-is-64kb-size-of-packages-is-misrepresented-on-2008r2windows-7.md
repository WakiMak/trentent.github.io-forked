---
id: 599
title: AppV 5 - minimum mounted file size is 64KB, size of packages is misrepresented on 2008R2/Windows 7
date: 2014-08-14T14:31:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/14/appv-5-minimum-mounted-file-size-is-64kb-size-of-packages-is-misrepresented-on-2008r2windows-7/
permalink: /2014/08/14/appv-5-minimum-mounted-file-size-is-64kb-size-of-packages-is-misrepresented-on-2008r2windows-7/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/appv-5-minimum-mounted-file-size-is.html
blogger_internal:
  - /feeds/7977773/posts/default/7118215323771425051
categories:
  - Blog
tags:
  - AppV
---
Our current configuration for our Citrix environment utilizes AppV 5 and mounts the packages to a different drive letter. � Our Citrix servers have the OS on C:\ and we changed the packageinstallationroot to D:\AppVData\PackageInstallationRoot and do full mounts of our AppV packages. � What we found is we have some packages consuming significantly more space then their extracted size would dictate. � I worked with Microsoft premier support to try and determine why, finally, after flaying for several months on why I finally got a reason and how we can avoid package bloat.

First the problem. � We have several packages that their total size should be less than the disk space it is consuming on our D:\. � We started running out of disk space earlier than anticipated so we investigated.

We packaged VMWare vSphere Client and included multiple versions of vSphere that maximizes the ability to connect to the servers/vCenter versions while minimizing the number of vSphere clients in the package. � In total, we sequenced 3 vSphere installs in one package. � The total package size is:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-DBvqzyPquFU/U-0SmUAAygI/AAAAAAAAAd4/P6dmN2rb2Pw/s1600/CompressedPackageSize.png"><img src="http://1.bp.blogspot.com/-DBvqzyPquFU/U-0SmUAAygI/AAAAAAAAAd4/P6dmN2rb2Pw/s1600/CompressedPackageSize.png" border="0" /></a>
</div>

&nbsp;

<pre class="lang:default decode:true ">0.98 GB (1,054,613,510 bytes)
</pre>

When we rename the .appv to .zip and extract the package the total size is:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-btOOdImYx0o/U-0TgHoZpcI/AAAAAAAAAeA/5lRXpOW3RtM/s1600/E-Drive-Properties.png"><img src="http://3.bp.blogspot.com/-btOOdImYx0o/U-0TgHoZpcI/AAAAAAAAAeA/5lRXpOW3RtM/s1600/E-Drive-Properties.png" width="320" height="205" border="0" /></a>
</div>

1.71GB on the AppVData for the extracted file. � 1.83GB used space on disk.

But, when we mount the .appv package using AppV the total size is:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-G0EYFS0AeVY/U-0TxiZaaMI/AAAAAAAAAeI/o1U8v40vv5Q/s1600/D-Drive-Properties.png"><img src="http://1.bp.blogspot.com/-G0EYFS0AeVY/U-0TxiZaaMI/AAAAAAAAAeI/o1U8v40vv5Q/s1600/D-Drive-Properties.png" width="320" height="205" border="0" /></a>
</div>

1.71GB on the AppVData folder, but <u>2.25GB</u> used space on disk. � A discrepancy of ~400MB. � With 10 packages that could be 4GB of wasted space across 60 Citrix servers it adds up fast.

So... � Where is this missing space and why does AppVData not report it correctly? � Well... it turns out it does report the used space correctly in Windows 8 / 2012. � The AppVData folder for the mounted application actually shows that it's 2.2GB when mounted on a Windows 8 / 2012 box. � Why the difference? � It turns out that when Appv 5 mounts applications it is mounting them using a 64KB allocation size. � In Windows 8 / 2012 when you get properties on a file in the mounted package it shows that it's 64KB used on disk. � On Windows 2008 / 7 it shows the file size used on disk, then it goes to the allocation size:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-2nJ9mW13Gck/U-0VTpjOh9I/AAAAAAAAAeQ/5VFfBvXOP6E/s1600/size-on-disk.png"><img src="http://4.bp.blogspot.com/-2nJ9mW13Gck/U-0VTpjOh9I/AAAAAAAAAeQ/5VFfBvXOP6E/s1600/size-on-disk.png" width="320" height="215" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Windows 2008 / 7 file sizes are incorrect for mounted packages
    </td>
  </tr>
</table>

If we delete a file under 64KB we will actually free up 64KB of space and Windows 2008 reports that correctly:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-DV1i4Tbu4Tg/U-0WWZFoHHI/AAAAAAAAAeY/QlRUR3_Afyo/s1600/free-space.png"><img src="http://4.bp.blogspot.com/-DV1i4Tbu4Tg/U-0WWZFoHHI/AAAAAAAAAeY/QlRUR3_Afyo/s1600/free-space.png" width="320" height="211" border="0" /></a>
</div>

19,045,400,576 bytes free before the 2,951 byte file is deleted, 19,045,466,112 bytes after.

19045400576 - 19045466112 = 65536 bytes.

So, in the end, it turns out that AppV 5 utilizes 64KB cluster size even if the allocation size on the disk is 4KB. � I'm not sure if having mismatched cluster sizes impacts the performance of AppV but the Microsoft support personnel implied that it could. � For AppV 5 you'll want to reduce the number of small files in your AppV packages to as few as possible to limit this space sucking overhead. � The missing space is "sparse" files, but fully mounted I wouldn't expect to have any sparse files. � So AppV is using sparse files to ensure a 64kb allocation size.

<pre class="lang:default decode:true ">Allocation Report:

Within these files there are:
    Compressed              : 0
        Total allocated     : 0 bytes
        Total size          : 0 bytes.
        Savings             : 0.00 %
    Sparse                  : 9106
        Total allocated     : 2318139392 bytes
        Total size          : 1838549977 bytes.
        Savings             : -26.09 %
    Encrypted               : 0
        Total allocated     : 0 bytes</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->