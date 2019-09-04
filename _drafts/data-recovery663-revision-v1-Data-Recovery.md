---
id: 1428
title: Data Recovery
date: 2016-04-28T23:43:22-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/663-revision-v1/
permalink: /2016/04/28/663-revision-v1/
---
My RAID-0+1 (2 sets of RAID-0 2TB HDD&#8217;s) just failed with two drives failing, both drives being from each stripe. &nbsp;The worst case scenario. &nbsp;The Intel RAID utility could not recover the array. &nbsp;I went and then downloaded some utilities to try and recreate the array but they all couldn&#8217;t determine what the array&#8217;s configuration was that I setup. &nbsp;Time to read up on [Microsoft&#8217;s clustering file share](http://technet.microsoft.com/en-us/library/jj134187.aspx#BKMK_Step1)s.

SO I went ahead and recreated the array using defaults and then tried the tool &#8220;[TestDisk](http://www.cgsecurity.org/wiki/TestDisk)&#8220;. &nbsp;TestDisk recognized my previous partition (which was GPT &#8212; Wow!). &nbsp;I had to manually set the type to NTFS then the tool could let me see the files within. &nbsp;I pulled out about 900GB of Hyper-V VHD&#8217;s and each one works perfectly. &nbsp;I am going to try recreating the partitions with that tool, but it&#8217;s telling me that the disk is too small, the previous partition is 4000MB and the current partition is 3800MB. &nbsp;This maybe accurate because the Intel RAID utility tries to reserve 5% at the end of the array for expansion/drives of different sizes so they can be added to the array. &nbsp;Once the recovery of the VHD&#8217;s completes, I will recreate the array with a slightly larger size then use TestDisk to recover the partitions and update it here if it worked or not.

UPDATE: I was not able to recover the partitions but I was able to recover the files successfully.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->