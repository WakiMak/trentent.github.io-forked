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

<div id="attachment_1489" style="width: 678px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1489" class="wp-image-1489 size-full" src="/wp-content/uploads/2016/06/VHD_fsinfo.png" alt="VHD_fsinfo" width="668" height="334" srcset="/wp-content/uploads/2016/06/VHD_fsinfo.png 668w, /wp-content/uploads/2016/06/VHD_fsinfo-300x150.png 300w" sizes="(max-width: 668px) 100vw, 668px" /></p> 
  
  <p id="caption-attachment-1489" class="wp-caption-text">
    16MB block size VHD file
  </p>
</div>

<div id="attachment_1490" style="width: 679px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1490" class="wp-image-1490 size-full" src="/wp-content/uploads/2016/06/VHDX_fsinfo.png" alt="VHDX_fsinfo" width="669" height="330" srcset="/wp-content/uploads/2016/06/VHDX_fsinfo.png 669w, /wp-content/uploads/2016/06/VHDX_fsinfo-300x148.png 300w" sizes="(max-width: 669px) 100vw, 669px" /></p> 
  
  <p id="caption-attachment-1490" class="wp-caption-text">
    32MB block size VHDX file
  </p>
</div>

I set my target devices to the different vDisks and set the 'Cache to RAM' feature to 4096MB.  Ideally, all writes should be to RAM but this will still tax the filesystem.

And what is the performance between the two?  I used the DiskSpd utility from Microsoft to measure the differences.

<div id="attachment_1492" style="width: 660px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1492" class="size-full wp-image-1492" src="/wp-content/uploads/2016/06/VHD_DiskSpeed.jpg" alt="VHD DiskSpd Test" width="650" height="431" srcset="/wp-content/uploads/2016/06/VHD_DiskSpeed.jpg 650w, /wp-content/uploads/2016/06/VHD_DiskSpeed-300x199.jpg 300w" sizes="(max-width: 650px) 100vw, 650px" /></p> 
  
  <p id="caption-attachment-1492" class="wp-caption-text">
    VHD DiskSpd Test
  </p>
</div>

<div id="attachment_1493" style="width: 664px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1493" class="size-full wp-image-1493" src="/wp-content/uploads/2016/06/VHDX_DiskSpeed.jpg" alt="VHDX DiskSpd Test" width="654" height="432" srcset="/wp-content/uploads/2016/06/VHDX_DiskSpeed.jpg 654w, /wp-content/uploads/2016/06/VHDX_DiskSpeed-300x198.jpg 300w" sizes="(max-width: 654px) 100vw, 654px" /></p> 
  
  <p id="caption-attachment-1493" class="wp-caption-text">
    VHDX DiskSpd Test
  </p>
</div>

<div id="attachment_1491" style="width: 1020px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1491" class="size-full wp-image-1491" src="/wp-content/uploads/2016/06/Summary.png" alt="Summary" width="1010" height="366" srcset="/wp-content/uploads/2016/06/Summary.png 1010w, /wp-content/uploads/2016/06/Summary-300x109.png 300w, /wp-content/uploads/2016/06/Summary-768x278.png 768w" sizes="(max-width: 1010px) 100vw, 1010px" /></p> 
  
  <p id="caption-attachment-1491" class="wp-caption-text">
    Summary
  </p>
</div>

The VHDX format appears to be around 7.5% faster in our setup.

The boot speed (the amount of time it takes the vDisk to power up and start the 'Citrix PVS Device Service') is even more dramatic:

<div id="attachment_1495" style="width: 465px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1495" class="size-full wp-image-1495" src="/wp-content/uploads/2016/06/VHD_Boot_Speed.png" alt="VHD Boot Speed" width="455" height="469" srcset="/wp-content/uploads/2016/06/VHD_Boot_Speed.png 455w, /wp-content/uploads/2016/06/VHD_Boot_Speed-291x300.png 291w" sizes="(max-width: 455px) 100vw, 455px" /></p> 
  
  <p id="caption-attachment-1495" class="wp-caption-text">
    VHD Boot Speed
  </p>
</div>

&nbsp;

<div id="attachment_1496" style="width: 462px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1496" class="size-full wp-image-1496" src="/wp-content/uploads/2016/06/VHDX_Boot_Speed.png" alt="VHDX Boot Speed" width="452" height="482" srcset="/wp-content/uploads/2016/06/VHDX_Boot_Speed.png 452w, /wp-content/uploads/2016/06/VHDX_Boot_Speed-281x300.png 281w" sizes="(max-width: 452px) 100vw, 452px" /></p> 
  
  <p id="caption-attachment-1496" class="wp-caption-text">
    VHDX Boot Speed
  </p>
</div>

How much of this is the tools vs the format?  I'm not sure, I didn't have the time to reverse image and upgrade a VHD to 7.7.  Regardless, the combination of upgrading to 7.7 from 7.1SP2 AND the VHDX format brought a dramatic boot time improvement and consistently faster disk speed.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->