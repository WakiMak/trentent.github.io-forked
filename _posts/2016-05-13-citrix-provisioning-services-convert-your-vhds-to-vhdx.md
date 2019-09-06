---
id: 1468
title: Citrix Provisioning Services - Convert your VHD's to VHDX
date: 2016-05-13T12:49:57-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1468
permalink: /2016/05/13/citrix-provisioning-services-convert-your-vhds-to-vhdx/
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - Provisioning Services
---
[Per my previous post](http://theorypc.ca/2016/05/13/citrix-provisioning-services-7-7-unable-to-mount-vhd-files/), we have been upgrading our storage backend and one of the features is 4K sector sizes being used. Â This is an issue as VHD files will not mount when they reside on 4K storage. Â Since we want this feature our two choices are to downgrade our storage to 512B sector sizes or move to the VHDX format. Â I would prefer to move to the VHDX format as it has a small performance advantage ([~7.5% was what I measured](http://theorypc.ca/wp-content/uploads/2016/05/14.png)) and future proofs us a little bit for when we upgrade our PVS servers to 2012.

The [VHDX](https://msdn.microsoft.com/en-us/library/windows/desktop/hh848035(v=vs.85).aspx)Â supports 512e on 4K storage which is only supported on the following operating systems:

<ul class="sbody-free_list">
  <li>
    Windows Vista
  </li>
  <li>
    Windows 7
  </li>
  <li>
    Windows Server 2008*
  </li>
  <li>
    Windows Server 2008 R2*
  </li>
  <li>
    Windows Server 2012
  </li>
  <li>
    Windows 8
  </li>
</ul>

So Windows Server 2003 is out, we are going to have to deal with not being able to mount their vDisks. Â For our Windows Server 2008 R2 servers we can convert our VHD files to VHDX. Â This is the process I have developed. Â Because we are going to be mounting the VHD and VHDX files you will need your files to reside on 512B storage. Â Without further ado, here are the steps to convert your VHD to VHDX:

  1. Create your VHDX file. Â Ensure to select 512B if you are migrating an operating system older than Server 2012.  
<img class="aligncenter size-full wp-image-1478" src="http://theorypc.ca/wp-content/uploads/2016/05/23.png" alt="23" width="370" height="559" srcset="http://theorypc.ca/wp-content/uploads/2016/05/23.png 370w, http://theorypc.ca/wp-content/uploads/2016/05/23-199x300.png 199w" sizes="(max-width: 370px) 100vw, 370px" /> 
  2. Mount your VHD file and your VHDX file. Â Format and set the partition as active for the VHDX if needed.  
<img class="aligncenter size-full wp-image-1471" src="http://theorypc.ca/wp-content/uploads/2016/05/16.png" alt="16" width="668" height="254" srcset="http://theorypc.ca/wp-content/uploads/2016/05/16.png 668w, http://theorypc.ca/wp-content/uploads/2016/05/16-300x114.png 300w" sizes="(max-width: 668px) 100vw, 668px" /> 
  3. Use a clone utility (DriveImage XML, Windows Server Backup or HDDRawCopy) to clone your VHD to the VHDX via the mounted paths.  
<img class="aligncenter size-full wp-image-1472" src="http://theorypc.ca/wp-content/uploads/2016/05/17.png" alt="17" width="796" height="382" srcset="http://theorypc.ca/wp-content/uploads/2016/05/17.png 796w, http://theorypc.ca/wp-content/uploads/2016/05/17-300x144.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/17-768x369.png 768w" sizes="(max-width: 796px) 100vw, 796px" /><img class="aligncenter size-full wp-image-1473" src="http://theorypc.ca/wp-content/uploads/2016/05/18.png" alt="18" width="794" height="381" srcset="http://theorypc.ca/wp-content/uploads/2016/05/18.png 794w, http://theorypc.ca/wp-content/uploads/2016/05/18-300x144.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/18-768x369.png 768w" sizes="(max-width: 794px) 100vw, 794px" /><img class="aligncenter size-full wp-image-1474" src="http://theorypc.ca/wp-content/uploads/2016/05/19.png" alt="19" width="779" height="516" srcset="http://theorypc.ca/wp-content/uploads/2016/05/19.png 779w, http://theorypc.ca/wp-content/uploads/2016/05/19-300x199.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/19-768x509.png 768w" sizes="(max-width: 779px) 100vw, 779px" /><img class="aligncenter size-full wp-image-1475" src="http://theorypc.ca/wp-content/uploads/2016/05/20.png" alt="20" width="779" height="515" srcset="http://theorypc.ca/wp-content/uploads/2016/05/20.png 779w, http://theorypc.ca/wp-content/uploads/2016/05/20-300x198.png 300w, http://theorypc.ca/wp-content/uploads/2016/05/20-768x508.png 768w" sizes="(max-width: 779px) 100vw, 779px" /> 
  4. Take this opportunity to defrag your cloned VHDX.  
<img class="aligncenter size-full wp-image-1476" src="http://theorypc.ca/wp-content/uploads/2016/05/21.png" alt="21" width="646" height="211" srcset="http://theorypc.ca/wp-content/uploads/2016/05/21.png 646w, http://theorypc.ca/wp-content/uploads/2016/05/21-300x98.png 300w" sizes="(max-width: 646px) 100vw, 646px" /> 
  5. Assign your vDisk to your target device.  
<img class="aligncenter size-full wp-image-1477" src="http://theorypc.ca/wp-content/uploads/2016/05/22.png" alt="22" width="485" height="421" srcset="http://theorypc.ca/wp-content/uploads/2016/05/22.png 485w, http://theorypc.ca/wp-content/uploads/2016/05/22-300x260.png 300w" sizes="(max-width: 485px) 100vw, 485px" /> 
  6. Done!

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->