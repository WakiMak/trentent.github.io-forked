---
id: 1463
title: Citrix Provisioning Services 7.7 - Unable to mount VHD files
date: 2016-05-13T08:51:34-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1463
permalink: /2016/05/13/citrix-provisioning-services-7-7-unable-to-mount-vhd-files/
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - Provisioning Services
---
We recently upgraded our storage where our PVS vDisk's reside.  I was attempting to inject a file using 'Mount vDisk' from the PVS Console and I got an error message:

<img class="alignnone size-full wp-image-1464 aligncenter" src="/wp-content/uploads/2016/05/1.png" alt="1" width="421" height="412" srcset="/wp-content/uploads/2016/05/1.png 421w, /wp-content/uploads/2016/05/1-300x294.png 300w, /wp-content/uploads/2016/05/1-50x50.png 50w" sizes="(max-width: 421px) 100vw, 421px" /> 

The text of the message states:  
"When accessing a new tape of a multivolume partition, the current block size is incorrect".

Google searching this message does not [bring any helpful](https://msdn.microsoft.com/en-us/library/ms838179.aspx) results.

I then tried to mount the vDisk via the command line:

<img class="aligncenter size-full wp-image-1465" src="/wp-content/uploads/2016/05/2.png" alt="2" width="1093" height="188" srcset="/wp-content/uploads/2016/05/2.png 1093w, /wp-content/uploads/2016/05/2-300x52.png 300w, /wp-content/uploads/2016/05/2-768x132.png 768w, /wp-content/uploads/2016/05/2-1024x176.png 1024w" sizes="(max-width: 1093px) 100vw, 1093px" /> 

Since we still had our 'older' storage I tried mounting the same vDisk.  It worked.  So something with our new storage was preventing us from mounting our VHD's.  We could still create new versions and boot our target devices off the new versions without issue, but mounting the vDisk's to add a file or change a single registry key is so much more efficient and faster.  We have 5 different datacenters with different storage at each, with 1 of the datacenters about to undergo another storage migration (so 6 different storage 'filers' in total).  I attempted to mount a vDisk from each:

<img class="aligncenter size-full wp-image-1466" src="/wp-content/uploads/2016/05/3.png" alt="3" width="1536" height="205" srcset="/wp-content/uploads/2016/05/3.png 1536w, /wp-content/uploads/2016/05/3-300x40.png 300w, /wp-content/uploads/2016/05/3-768x103.png 768w, /wp-content/uploads/2016/05/3-1024x137.png 1024w" sizes="(max-width: 1536px) 100vw, 1536px" /> 

A pattern immediately emerged on the far right of this procmon trace.  The 'filers' that allowed us to mount the VHD file had the 'QuerySizeInformationValue' report back a 'BytesPerSector' of 512.  The filers that failed had a BytesPerSector of 4,096.  Immediately, it became obvious that the new storage had a 4K format and the older storage had 512B format.  [Microsoft has a statement about VHD files and 4K support](https://support.microsoft.com/en-us/kb/2515143):

> ### Effect when hosting VHDs on native 4K disks {.sbody-h3}
> 
> The VHD driver today assumes that the size of the physical sector of the disk is 512 bytes and issues 512-bytes IOs. This makes the VHD driver incompatible with these disks. Therefore, the VHD stack does not open the VHD files on physical 4 KB sector disks.

Well, there you go.  You cannot mount VHD files when they reside on 4K storage.  Fortunately, PVS 7.7 supports VHDX files with 512e support with Windows 2008R2 so we can move our VHD's to VHDX's.  I will document how to do so in my next post.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->