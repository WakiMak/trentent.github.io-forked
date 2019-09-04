---
id: 578
title: Becareful of your AVHD to VHD chains with Citrix PVS with multiple sites
date: 2015-02-05T02:07:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/02/05/becareful-of-your-avhd-to-vhd-chains-with-citrix-pvs-with-multiple-sites/
permalink: /2015/02/05/becareful-of-your-avhd-to-vhd-chains-with-citrix-pvs-with-multiple-sites/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/02/becareful-of-your-avhd-to-vhd-chains.html
blogger_internal:
  - /feeds/7977773/posts/default/7401710258012772511
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
---
Citrix PVS is a great product. &nbsp;With a single VHD file you can boot multiple machines and with the new RAM caching technology introduced in PVS 7.1 it's super fast.

We have Citrix PVS 7.1 setup across 5 data centres in 3 geographically disperse regions with 2 primary sites each having a primary datacenter and a DR datacenter. &nbsp;Each datacenter has high-speed local share for our PVS images. &nbsp;Our Active Directory architecture is 2003 level. &nbsp;Our PVS setup is configured to stream the vDisks from a local file share. &nbsp;This information is important to add context for our process.

I found an issue in one of our datacenters when we had an issue and had to reboot some VM's. &nbsp;Essentially the issue is our target devices were booting slow. &nbsp;Like really, really, really slow. &nbsp;In addition to that, once they did boot; logging into them was slow. &nbsp;Doing any action once you were logged in was slow. &nbsp;But it did seem to get faster the more you click'ed and prodded your way around the system. &nbsp;For instance, clicking the 'Start' button took 15 seconds to open the first time but was normal afterwards, but clicking on any sub-folders was slow with the same symptoms.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://1.bp.blogspot.com/-i08pUpT5U3M/VNMLf68n8lI/AAAAAAAAAtw/7EB9NBEX1mM/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.19.09%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://1.bp.blogspot.com/-i08pUpT5U3M/VNMLf68n8lI/AAAAAAAAAtw/7EB9NBEX1mM/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.19.09%2BPM.png" height="320" width="312" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is a poor performing VM. &nbsp;Notice the high number of retries, slow Throughput and long Boot Time
    </td>
  </tr>
</table>

Conversely, connecting to a target device at the other city showed these statistics:

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-_9vtwdOR28E/VNMMWO8DOjI/AAAAAAAAAuA/b-DKfbTHdu0/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.23.06%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://2.bp.blogspot.com/-_9vtwdOR28E/VNMMWO8DOjI/AAAAAAAAAuA/b-DKfbTHdu0/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.23.06%2BPM.png" height="320" width="312" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Example of a much, much better performing VM. &nbsp;14 second boot time, throughput about 10x faster than the slower VM and ZERO retries.
    </td>
  </tr>
</table>

So we have an issue. &nbsp;These two separate cities are configured nearly identically but one is suffering severely in performance. &nbsp;We looked at the VMHost that it the VM was hosted at, but moving it to different hosts didn't seem to make any difference. &nbsp;I then RDP'ed to our VM and launched ProcessMonitor so I could watch the bootup process. &nbsp;This is what I saw:

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-q5_AxT8b2ow/VNMN1V3SauI/AAAAAAAAAuM/TcSs5QUhrvo/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B9.29.32%2BAM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-q5_AxT8b2ow/VNMN1V3SauI/AAAAAAAAAuM/TcSs5QUhrvo/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B9.29.32%2BAM.png" height="199" width="320" /></a>
</div>

chfs04 is the local file server hosting our vDisk. &nbsp;citrixnas01 is a remote file server on a WAN connection in the datacenter in the other city. &nbsp;For some reason, PVS is reading and sharing the \*versioned\* avhd file that resides on the local file share, but the base VHD file it is reading from citrixnas01. &nbsp;This is, obviously, a huge issue. &nbsp;Reading the base image over a WAN probably will result in the poor performance we are experiencing and the high retry counts for packets.

But why is it going over the WAN? &nbsp;It turns out that the avhd file contains a section in it's header that [describes the location of the parent VHD file](http://blogs.msdn.com/b/robertvi/archive/2009/12/17/getting-some-more-info-about-my-vhd-and-avhd-files.aspx). &nbsp;PVS is simply using the native sequence built in to the VHD spec for the chain'ed disks.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-vyiiZwAVha4/VNMUoaMy9zI/AAAAAAAAAuc/YXT_xujFsR0/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.52.25%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://3.bp.blogspot.com/-vyiiZwAVha4/VNMUoaMy9zI/AAAAAAAAAuc/YXT_xujFsR0/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.52.25%2BPM.png" height="323" width="640" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Hex editing the avhd file reveals the chain path
    </td>
  </tr>
</table>

(Un)Fortunately for us, our PVS servers can read the file share across the WAN and pull and cache data locally so the speedups tended to gain the longer the VM was in use. &nbsp;In order to fix this issue immediately, we edited our hosts file on the PVS server to point to the local file server for citrixnas01.

After executing that change I rebooted one of the target devices in the \*slow\* site. 

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://1.bp.blogspot.com/-vfWUmYVBWKQ/VNMXkzWZNII/AAAAAAAAAuo/yrgcdyjh4f8/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.18.17%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://1.bp.blogspot.com/-vfWUmYVBWKQ/VNMXkzWZNII/AAAAAAAAAuo/yrgcdyjh4f8/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B11.18.17%2BPM.png" height="320" width="313" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Throughput is now *much* better, but the number of retries is still concerning
    </td>
  </tr>
</table>

Now, the tricky thing about this issue is we thought we had it covered because we configured each site to have it's own store:

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-QqCp_6pOBrc/VNMZXKZPvJI/AAAAAAAAAu0/oCT6UE3fFQk/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B9.28.47%2BAM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-QqCp_6pOBrc/VNMZXKZPvJI/AAAAAAAAAu0/oCT6UE3fFQk/s1600/Screen%2BShot%2B2015-02-04%2Bat%2B9.28.47%2BAM.png" height="246" width="400" /></a>
</div>

Our thoughts were that this path is what the PVS service would look for when trying to find VHD's and AVHD's. &nbsp;So when it would look for &Pn01.6.vhd it would look in that store. &nbsp;Obviously, this line of thinking is not correct. &nbsp;So it is important if you have sites that are distant that the path you use to create your version will correspond to the fastest, local, share in all your sites. &nbsp;For us, our folder structure is identical in all sites so creating a host file entry pointing citrixnas01 to the local file share works in our scenario to start with.

EDIT - I should also note that you can see the incorrect file paths in process explorer as well. &nbsp;Changing the host file wasn't enough, we also needed to restart the streaming service as the streaming service held cached data on how to reach the files across the WAN. &nbsp;Process Explorer can show you the VHD files that the stream service has access to and where (under files and handles):

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://4.bp.blogspot.com/-UfJk71_qijk/VNNV04kn-XI/AAAAAAAAAvE/R_HYTT7qToU/s1600/Screen%2BShot%2B2015-02-05%2Bat%2B4.19.31%2BAM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://4.bp.blogspot.com/-UfJk71_qijk/VNNV04kn-XI/AAAAAAAAAvE/R_HYTT7qToU/s1600/Screen%2BShot%2B2015-02-05%2Bat%2B4.19.31%2BAM.png" height="76" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Citrixnas01 shouldn't be in there... &nbsp;Host file has been changed...
    </td>
  </tr>
</table>

After a streaming service restart:

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://1.bp.blogspot.com/-ZUyHKeQQ11E/VNNWZ-WjMKI/AAAAAAAAAvM/SmkhG3HNZDE/s1600/Screen%2BShot%2B2015-02-05%2Bat%2B4.39.05%2BAM.png" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://1.bp.blogspot.com/-ZUyHKeQQ11E/VNNWZ-WjMKI/AAAAAAAAAvM/SmkhG3HNZDE/s1600/Screen%2BShot%2B2015-02-05%2Bat%2B4.39.05%2BAM.png" height="183" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Muuuuch better.
    </td>
  </tr>
</table>



<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->