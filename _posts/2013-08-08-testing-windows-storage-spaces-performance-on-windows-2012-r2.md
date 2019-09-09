---
id: 631
title: Testing Windows Storage Spaces Performance on Windows 2012 R2
date: 2013-08-08T15:53:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/08/08/testing-windows-storage-spaces-performance-on-windows-2012-r2/
permalink: /2013/08/08/testing-windows-storage-spaces-performance-on-windows-2012-r2/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/08/testing-windows-storage-spaces.html
blogger_internal:
  - /feeds/7977773/posts/default/2048100699374597602
categories:
  - Blog
  - Uncategorized
tags:
  - 2012R2
  - Performance
---
Windows Storage Spaces parity performance on Windows Server 2012 is terrible.  Microsoft's justification for it is that it's not meant to be used for anything except ["workloads that are almost exclusively read-based, highly sequential, and require resiliency, or workloads that write data in large sequential append blocks (such as bulk backups)."](http://blogs.technet.com/b/mspfe/archive/2013/02/25/why-windows-server-2012-parity-storage-spaces-might-perform-slowly.aspx)

I find this statement to be a bit amusing because trying to back up anything @ 20MB/sec takes forever.  If you setup a Storage Spaces parity volume at 12TB (available space) and you have 10TB of data to copy to it just to get it going it will take you 8738 seconds, or 145 hours, or **<u>6 straight days</u>**.  I have no idea who thought anything like that would be acceptable.  Maybe they want to adjust their use case to volumes under 1GB?

Anyways, with 2012R2 there maybe some feature enhancements including a new feature for storage spaces; 'tiered storage' and write back caching.  This allows you to use fast media like flash to be  a staging ground so writes complete faster and then the writes to the fast media can transfer that data to the slower storage at a time that is more convient.  Does this fix the performance issues in 2012?  How does the new 2-disk parity perform?

To test I made two VM's.  One a generic 2012 and one a 2012R2.  They have the exact same volumes, 6x10GB volumes in total.  The volumes are broken down into 4x10GB volumes on a 4x4TB RAID-10 array, 1x10GB volume on a 256GB Samsung 840 Pro SSD and 1x10GB volume on a RAMDisk (courtesy of DataRAM).  Performance for each set of volumes is:

4x4TB RAID-10 -> 220MB/s write, 300MB/s read  
256MB Samsung 840 Pro SSD -> ~250MB/s write, 300MB/s read  
DataRAM RAMDisk -> 4000MB/s write, 4000MB/s read

The Samsung SSD volume has a small sequential write advantage, it should have a significant seek advantage, as well since the volume is dedicated on the Samsung it should be significantly faster as you could probably divide by 6 to get the individual performance of the 4x10GB volumes on the single RAID.  The DataRAM RAMDisk drive should crush both of them for read and write performance under all situations.  For my weak testing, I only tested sequential performance.

First thing I did was create my storage pool with my 6 volumes that reside on the RAID-10.  I used this powershell script to create them:

<pre class="lang:ps decode:true ">Get-PhysicalDisk
$disks = Get-PhysicalDisk |? {$_.CanPool -eq $true}
New-StoragePool -StorageSubSystemFriendlyName *Spaces* -FriendlyName TieredPool -PhysicalDisks $disks
Get-StoragePool -FriendlyName TieredPool | Get-PhysicalDisk | Select FriendlyName, MediaType
Set-PhysicalDisk -FriendlyName PhysicalDisk1 -MediaType HDD
Set-PhysicalDisk -FriendlyName PhysicalDisk2 -MediaType HDD
Set-PhysicalDisk -FriendlyName PhysicalDisk3 -MediaType HDD
Set-PhysicalDisk -FriendlyName PhysicalDisk4 -MediaType HDD
Set-PhysicalDisk -FriendlyName PhysicalDisk5 -MediaType HDD
Set-PhysicalDisk -FriendlyName PhysicalDisk6 -MediaType HDD</pre>

The first thing I did was create a stripe disk to determine my maximum performance amoung my 6 volumes.  I mapped to my DataRAM Disk drive and copied a 1.5GB file from it using xcopy /j

Performance to the stripe seemed good.  About 1.2Gb/s (150MB/s)

I then deleted the volume and recreated it as a single parity drive.

Executing the same command xcopy /j I seemed to be averaging around 348Mb/s (43.5MB/s)

This is actually faster than what I remember getting previously (around 20MB/s) and this is through a VM.

I then deleted the volume and recreated it as a dual parity drive.  To get the dual parity drive to work I actually had to add a 7th disk.  5 nor 6 would work as it would tell me I lacked sufficient space.

Executing the same command xcopy /j I seemed to be averaging around 209Mb/s (26.1MB/s)

I added my SSD volume to the VM and deleted the storage spaces volume.  I then added my SSD volume to the pool and recreated it with "tiered" storage now.

When I specified to make use the SSD as the tiered storage it removed my ability to create a parity volume.  So I created a simple volume for this testing.

Performance was good.  I achieved 2.0Gb/s (250MB/s) to the volume.

<div style="clear: both; text-align: center;">
</div>

With the RAMDisk as the SSD tier I achieved 3.2Gb/s (400MB/s).  My 1.5GB file may not be big enough to ramp up to see the maximum speed, but it works.  Tiered storage make a difference, but I didn't try to "overfill" the tiered storage section.

I wanted to try the write-back cache with the parity to see if that helps.  I found [this page](http://www.miru.ch/2013/07/creating-tiered-storage-spaces-in-server-2012-r2/) that tells me it can only be enabled through PowerShell at this time.

<pre class="lang:ps decode:true ">$ssd_tier = New-StorageTier -StoragePoolFriendlyName TieredPool -FriendlyName SSD_Tier -MediaType SSD
$hdd_tier = New-StorageTier -StoragePoolFriendlyName TieredPool -FriendlyName HDD_Tier -MediaType HDD
$vd2 = New-VirtualDisk -StoragePoolFriendlyName TieredPool -FriendlyName HD -Size 24GB -ResiliencySettingName Parity -ProvisioningType Fixed -WriteCacheSize 8GB</pre>

I enabled the writecache with both my SSD and RAMDisk as being a part of the pool and the performance I got for copying the 1.5GB file was 1.8Gb/s (225MB/s)

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://2.bp.blogspot.com/-6lf46fv24oM/UgQQoNNnIqI/AAAAAAAAAXs/34J_mdbcn6I/s1600/1.png"><img src="http://2.bp.blogspot.com/-6lf46fv24oM/UgQQoNNnIqI/AAAAAAAAAXs/34J_mdbcn6I/s320/1.png" width="320" height="240" border="0" /></a>
</div>

And this is on a single parity drive!  Even though the copy completed quickly I could see in Resource Manager the copy to the E: drive did not stop, after hitting the cache at ~200MB/s it dropped down to ~45-30MB/s for several seconds afterwards.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-0NiMsHSskYM/UgQSMFP6XiI/AAAAAAAAAYE/7xhWq5TTHMs/s1600/3.png"><img src="http://4.bp.blogspot.com/-0NiMsHSskYM/UgQSMFP6XiI/AAAAAAAAAYE/7xhWq5TTHMs/s320/3.png" width="320" height="240" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      You can see xcopy.exe is still going but there is no more network activity.  The total is in Bytes per second and you can see it's writing to the E: drive at about 34.13MB/s
    </td>
  </tr>
</table>

I imagine this is the 'Microsoft Magic' going on where the SSD/write cache is now purging out to the slower disks.

I removed the RAMDisk SSD to see what impact it may have if it's just hitting the stock SSD.

Removing the RAMDisk SSD and leaving the stock SSD I hit about 800Mb/s (100MB/s).

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-YVdqDKQ0Ozg/UgQRPA6gLHI/AAAAAAAAAX0/3Nb1xtq4H3o/s1600/2.png"><img src="http://1.bp.blogspot.com/-YVdqDKQ0Ozg/UgQRPA6gLHI/AAAAAAAAAX0/3Nb1xtq4H3o/s320/2.png" width="320" height="240" border="0" /></a>
</div>

This is very good!  I reduced the writecache size to see what would happen if the copy exceeded the cache...  I recreated the volume with the writecachesize at 100MB

<pre class="lang:ps decode:true ">$vd2 = New-VirtualDisk -StoragePoolFriendlyName TieredPool -FriendlyName HD -Size 24GB -ResiliencySettingName Parity -ProvisioningType Fixed -WriteCacheSize 8GB</pre>

As soon as the writecache filled up it was actually a little slower then before, 209Mb/s (26.1MB/s).  100MB just isn't enough to help.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-i9sI7qqKZ8I/UgQSydqlqxI/AAAAAAAAAYM/S1iL-eM0XrE/s1600/4.png"><img src="http://2.bp.blogspot.com/-i9sI7qqKZ8I/UgQSydqlqxI/AAAAAAAAAYM/S1iL-eM0XrE/s320/4.png" width="320" height="240" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      100MB of cache is just not enough to help
    </td>
  </tr>
</table>

Here I am now at the end.  It appears tiered storage only helps mirrored or stripe volumes.  Since they are the fastest volumes anyways, it appears the benefits aren't as high as they could be.  With parity drives though, the writecachesetting has a profound impact in the initial performance of the system.  As long as whatever fills the cache as enough time to purge to disk in the inbetweens you'll be ok.  By that I mean without a SSD present and write cache at default a 1GB file will copy over at 25MB/s in 40 seconds.  With a 100MB SSD cache present it will take 36 seconds because once the cache is full it will be bottlenecked by how fast it can empty itself.  Even worse, in my small scale test, it hurt performance by about 50%.  A large enough cache probably won't encounter this issue as long as there is sufficient time for it to clear.  Might be worthwhile to invest in a good UPS as well.  If you have a 100GB cache that is near full and the power goes out it will take about 68 minutes for the cache to finish dumping itself to disk.  At 1TB worth of cache you could be looking at 11.37 hours.  I'm not sure how Server 2012R2 deals with a power outage on the write cache, but since it's a part of the pool I imagine on reboot it will just pick up where it left off...?

Anyways, with storage spaces I do have to give Microsoft kudos.  It appears they were able to come close to doubling the performance on the single parity to ~46MB/s.  On the dual-parity it's at about 26MB/s under my test environment.  With the write cache everything is exteremely fast until the write cache becomes full.  After that it's painful.  So it's very important to size up your cache appropriately.  I have a second system with 4x4TB drives in a storage pool mirrored configuration.  Once 2012 R2 comes out I suspect I'll update to it and change my mirror into a single parity with a 500GB SSD cache drive.  Once that happens I'll try to remember to retest these performance numbers and we'll see what happens 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->