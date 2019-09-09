---
id: 608
title: How To Convert a 16MB block size PVS VHD to a 2MB PVS VHD file using Hyper-V
date: 2014-05-21T12:54:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/05/21/how-to-convert-a-16mb-block-size-pvs-vhd-to-a-2mb-pvs-vhd-file-using-hyper-v/
permalink: /2014/05/21/how-to-convert-a-16mb-block-size-pvs-vhd-to-a-2mb-pvs-vhd-file-using-hyper-v/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/05/how-to-convert-16mb-block-size-pvs-vhd.html
blogger_internal:
  - /feeds/7977773/posts/default/138823970653467936
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
---
If you've created a 16MB block size VHD file using Citrix PVS you've probably experienced that these disks are a touch more difficult to manage than the 2MB ones.  With 16MB block size VHD's you cannot mount them in Windows natively, compact them, and manipulate them with regular VHD tools.  The only tool that I've been able to manipulate them with is PVS, specifically CVHDMOUNT.EXE.  This allows you to mount the VHD file as a hard disk, but even so you cannot manipulate the disk with lots of tools; the disk isn't recognized at all.  This includes tools like Disk2VHD that you would think would make it easy to make it into a VHD.  Not so.

In order to convert from a 16MB block size PVS VHD you need to do the following steps:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-8l2YCwt0_TU/U3z2Af3RMQI/AAAAAAAAAb4/VJ_aeKXpgeU/s1600/CVHDMOUNT.PNG"><img src="http://1.bp.blogspot.com/-8l2YCwt0_TU/U3z2Af3RMQI/AAAAAAAAAb4/VJ_aeKXpgeU/s1600/CVHDMOUNT.PNG" width="320" height="90" border="0" /></a>
</div>

1) Mount the VHD file using CVHDMount.exe (you may need to install the Provisioning Server role on it to get the CVHDMount.exe tools)

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-Dr-4ryDVL4M/U3z2KkeLZzI/AAAAAAAAAcs/qlFV3l3__wA/s1600/DiskMgmt.png"><img src="http://3.bp.blogspot.com/-Dr-4ryDVL4M/U3z2KkeLZzI/AAAAAAAAAcs/qlFV3l3__wA/s1600/DiskMgmt.png" width="166" height="320" border="0" /></a>
</div>

2) Go into Disk Management and set your disk to "Offline"

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-AdvgXtQVByY/U3z2CUhdmNI/AAAAAAAAAcU/QR6cTvb2WVM/s1600/HYPERV-NEWDISK.PNG"><img src="http://3.bp.blogspot.com/-AdvgXtQVByY/U3z2CUhdmNI/AAAAAAAAAcU/QR6cTvb2WVM/s1600/HYPERV-NEWDISK.PNG" width="320" height="202" border="0" /></a>
</div>

3) Open Hyper-V, right-click your server and go "New > Hard Disk..."

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-W_f_Xv_3kY4/U3z2CkEwjgI/AAAAAAAAAcc/frcFfgVVchc/s1600/NEWDISK-LOCATION.PNG"><img src="http://2.bp.blogspot.com/-W_f_Xv_3kY4/U3z2CkEwjgI/AAAAAAAAAcc/frcFfgVVchc/s1600/NEWDISK-LOCATION.PNG" width="320" height="233" border="0" /></a>
</div>

4) Choose the location and filename to save it.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-zBLPnf-UGRc/U3z2B4IGMSI/AAAAAAAAAcY/eBMX7oUGi18/s1600/COPY-FROM-DISK.PNG"><img src="http://4.bp.blogspot.com/-zBLPnf-UGRc/U3z2B4IGMSI/AAAAAAAAAcY/eBMX7oUGi18/s1600/COPY-FROM-DISK.PNG" width="320" height="232" border="0" /></a>
</div>

5) Choose to copy from the PHYSICALDISK that corresponds to your VHD you mounted.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-9zP-rqA81SM/U3z2B1AVZQI/AAAAAAAAAcg/zE_nsIuph2s/s1600/FINISH.PNG"><img src="http://2.bp.blogspot.com/-9zP-rqA81SM/U3z2B1AVZQI/AAAAAAAAAcg/zE_nsIuph2s/s1600/FINISH.PNG" width="320" height="232" border="0" /></a>
</div>

6) Click Finish.  And now you're done!  🙂

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->