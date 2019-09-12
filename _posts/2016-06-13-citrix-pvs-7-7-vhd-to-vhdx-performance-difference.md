---
id: 1488
title: Citrix PVS 7.7 VHD to VHDX performance difference
date: 2016-06-13T09:28:32-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1488
permalink: /2016/06/13/citrix-pvs-7-7-vhd-to-vhdx-performance-difference/
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - Provisioning Services
  - PVS
---
I was asked to justify upgrading our PVS vDisks to VHDX from VHD.  There are a few 'feature' / technical reasons:

  1. Use of native tools to mount/compress VHDX from Windows Server 2012.  [VHD files created with 16MB block sizes require a custom Citrix tool](http://theorypc.ca/2014/05/21/how-to-convert-a-16mb-block-size-pvs-vhd-to-a-2mb-pvs-vhd-file-using-hyper-v/) which does not do compress.
  2. VHDX is the format 'forward'.
  3. [VHDX is supposed to perform better](https://blogs.technet.microsoft.com/askpfeplat/2013/09/08/why-you-want-to-be-using-vhdx-in-hyper-v-whenever-possible-and-why-its-important-to-know-your-baselines/).

Test setup: VHD file is Citrix PVS 7.1SP2, [VHDX file is a clone of the VHD with the tools upgraded to 7.7](http://theorypc.ca/2016/05/13/citrix-provisioning-services-convert-your-vhds-to-vhdx/).

So I know VHDX is supposed to perform better but I was curious by how much.  Apparently, the modification Citrix made to the VHD format to a 16MB block size is '4K' aligned as well.

fsutil fsinfo ntfsinfo C: reports the following for the different vDisks:

![](/wp-content/uploads/2016/06/VHD_fsinfo.png)  
16MB block size VHD file

![](/wp-content/uploads/2016/06/VHDX_fsinfo.png)  
32MB block size VHDX file

I set my target devices to the different vDisks and set the 'Cache to RAM' feature to 4096MB.  Ideally, all writes should be to RAM but this will still tax the filesystem.

And what is the performance between the two?  I used the DiskSpd utility from Microsoft to measure the differences.

![](/wp-content/uploads/2016/06/VHD_DiskSpeed.jpg)  
VHD DiskSpd Test

![](/wp-content/uploads/2016/06/VHDX_DiskSpeed.jpg)  
VHDX DiskSpd Test

![](/wp-content/uploads/2016/06/Summary.png)  
Summary

The VHDX format appears to be around 7.5% faster in our setup.

The boot speed (the amount of time it takes the vDisk to power up and start the 'Citrix PVS Device Service') is even more dramatic:

![](/wp-content/uploads/2016/06/VHD_Boot_Speed.png)  
VHD Boot Speed


![](/wp-content/uploads/2016/06/VHDX_Boot_Speed.png)  
VHDX Boot Speed

How much of this is the tools vs the format?  I'm not sure, I didn't have the time to reverse image and upgrade a VHD to 7.7.  Regardless, the combination of upgrading to 7.7 from 7.1SP2 AND the VHDX format brought a dramatic boot time improvement and consistently faster disk speed.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->