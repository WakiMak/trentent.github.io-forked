---
id: 1901
title: Lets Make PVS Target Device Booting Great Again (Part 2)
date: 2017-01-05T09:00:35-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1901
permalink: /2017/01/05/lets-make-pvs-target-device-booting-great-again-part-2/
image: /wp-content/uploads/2017/01/icon.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - procmon
  - Provisioning Services
  - PVS
  - Target Device
  - Windows 10
---
[Continuing on from Part 1](https://theorypc.ca/2016/12/30/lets-make-pvs-target-device-booting-great-again-part-1/), we are looking to optimize the PVS boot process to be as fast as it possibly can be.  In Part 1 we implemented Jumbo Frames across both the PVS target device and the PVS server and discovered that Jumbo Frames only applies to the portion where BNIStack kicks in.

In this part we are going to examine the option "I/O burst size (KB)"  This policy is explained in the help file:

> I/O burst size - The number of bytes that will be transmitted in a single read/write transaction before an ACK is sent from the server or device. The larger the IO burst, the faster the throughput to an individual device, but the more stress placed on the server and network infrastructure. Also, larger IO Bursts increase the likelihood of lost packets and costly retries. Smaller IO bursts reduce single client network throughput, but also reduce server load. Smaller IO bursts also reduce the likelihood of retries. IO Burst Size / MTU size must be <= 32, i.e. only 32 packets can be in a single IO burst before a ACK is needed.

What are these ACK's and can we see them?  We can.  They are UDP packets sent back from the target device to the PVS server.  If you open Procmon on the PVS server and startup a target device an ACK looks like so:

![](/wp-content/uploads/2016/12/48ByteUDPReceieve.png)  
These highlighted 48byte UDP Receive packets? They are the ACKS

And if we enable the disk view with the network view:

<img class="aligncenter size-full wp-image-1903" src="/wp-content/uploads/2016/12/32KB_HDD_READ.png" alt="" width="1308" height="921" srcset="/wp-content/uploads/2016/12/32KB_HDD_READ.png 1308w, /wp-content/uploads/2016/12/32KB_HDD_READ-300x211.png 300w, /wp-content/uploads/2016/12/32KB_HDD_READ-768x541.png 768w" sizes="(max-width: 1308px) 100vw, 1308px" /> 

&nbsp;

With each 32KB read of the hard disk we send out 24 packets, 23 at 1464 bytes and 1 at 440 bytes.  Add them all together and we get 34,112 Bytes of data.  This implies an overall overhead of 1344 bytes per sequence of reads or 56 bytes per packet.  I confirmed it's a per-packet overhead by looking at a different read event at a different size:

<img class="aligncenter size-full wp-image-1904" src="/wp-content/uploads/2016/12/DifferentSizedReads.png" alt="" width="1309" height="334" srcset="/wp-content/uploads/2016/12/DifferentSizedReads.png 1309w, /wp-content/uploads/2016/12/DifferentSizedReads-300x77.png 300w, /wp-content/uploads/2016/12/DifferentSizedReads-768x196.png 768w" sizes="(max-width: 1309px) 100vw, 1309px" /> 

If we look at the first read event (8,192) we can see there is 6 packets, 5 at 1464 and one at 1208, totaling 8528 bytes of traffic.  8528 - 8192 = 336 bytes of overhead / 6 packets = 56 bytes.

The same happens with the 16,384 byte read next in the list.  12 packets, 11 at 1464 and one at 952, totaling 17,056.  17056 - 16384 = 672 bytes of overhead / 12 packets = 56 bytes.

So it's consistent.  For every packet at the standard 1506 MTU you are losing 3.8% to overhead.  But there is secretly more overhead than just that.  For every read there is a 48 byte ACK overhead on top.  Admittedly, it's not much; but it's present.

And how does this look with Jumbo Frames?

<img class="aligncenter size-full wp-image-1905" src="/wp-content/uploads/2016/12/Jumbo_Overhead.png" alt="" width="1309" height="124" srcset="/wp-content/uploads/2016/12/Jumbo_Overhead.png 1309w, /wp-content/uploads/2016/12/Jumbo_Overhead-300x28.png 300w, /wp-content/uploads/2016/12/Jumbo_Overhead-768x73.png 768w" sizes="(max-width: 1309px) 100vw, 1309px" /> 

For a 32KB read we satisfied the request in 4 packets.  3 x 8972 bytes and 1 at 6076 bytes totalling 32,992 bytes of transmitted data.  Subtracting the transmitted data from what is really required 32,992-32,768 = 224 bytes of overhead or...  56 bytes per packet 

This amounts to a measly 0.6% of overhead when using jumbo frames (an immediate 3% gain!).

But what about this 32KB value.  What happens if we adjust it longer (or shorter)?

Well, there is a limitation that handicaps us...  even if we use Jumbo Frames.  It is stated here:

> IO Burst Size / MTU size must be <= 32, i.e. <span style="text-decoration: underline;"><strong>only 32 packets can be in a single IO burst</strong></span> before a ACK is needed

_Because Jumbo Frames don't occur until after the BNIStack kicks in, we are limited to working out this math at the 1506 MTU size._

The caveat of this is the size isn't actually the MTU size of 1506.  The math is based on the data that fits within, which is 1464 bytes.  Doing the math in reverse gives us 1464 x 32 = 45056 bytes.  This equals a clear 44K (45056 /1024) maximum size.  Setting IO/Burst to 44K and the target device still boots.  Counting the packets, there are 32 packets.

<img class="aligncenter size-full wp-image-1913" src="/wp-content/uploads/2017/01/45K.png" alt="" width="1196" height="481" srcset="/wp-content/uploads/2017/01/45K.png 1196w, /wp-content/uploads/2017/01/45K-300x121.png 300w, /wp-content/uploads/2017/01/45K-768x309.png 768w" sizes="(max-width: 1196px) 100vw, 1196px" /> 

So if we up the IO/Burst by 1K to 45K (45*1024 = 46,080 bytes) will it still boot?

[<img class="aligncenter wp-image-1914 size-large" src="/wp-content/uploads/2017/01/No_Boot-1600x774.png" width="1140" height="551" srcset="/wp-content/uploads/2017/01/No_Boot-1600x774.png 1600w, /wp-content/uploads/2017/01/No_Boot-300x145.png 300w, /wp-content/uploads/2017/01/No_Boot-768x372.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />](/wp-content/uploads/2017/01/No_Boot.png)

It does <span style="text-decoration: underline;"><strong>not</strong> </span>boot.  This enforces a <span style="text-decoration: underline;"><strong>hard limit of 44K</strong> </span>for I/O Burst until the 1st stage supports a larger MTU size.  I have only explored EFI booting, so I suppose it's possible another boot method allows for larger MTU?

The reads themselves are split now, hitting the 'version' and the 'base' with the base being 25,600 + 20,480 for the version (46,080 bytes).  I believe this is normal for versioning though.

So what's the recommendation here?

Good question.  Citrix defaults to 32K I/O Burst Size.  If we break the operation of the burst size we have 4 portions:

  1. Hard drive read time
  2. Packet send time
  3. Acknowledgement of receipt
  4. Turnaround time from receipt to next packet send

The times that I have for each portion at a 32K size appear to be (in milliseconds):

  1. 0.3
  2. 0.5
  3. 0.2
  4. 0.4

A total time of ~1.4ms per read transaction at 32K.

For 44K I have the following:

  1. 0.1
  2. 0.4
  3. 0.1
  4. 0.4

For a total time of ~1.0ms per read transaction at 44K.

I suspect the 0.4ms difference could be well within a margin of error of my hand based counting.  I took my numbers from a random sampling of 3 transactions, and averaged.  I cannot guarantee they were at the same spot of the boot process.

However, it appears the difference between them is close to negligible.  Question that must be posed is what's the cost of a 'retry' or a missed or faulty UDP packet?  From the evidence I have it should be fairly small, but I haven't figured out a way to test or detect what the turnaround time of a 'retry' is yet.

Citrix has a utility that gives you some information on what kind of gain you might get.  It's called 'Stream Console' and it's available in the Provisioning Services folder:

&nbsp;

![](/wp-content/uploads/2017/01/StreamConsole.png)  
With 4K I/O burst it does not display any packets sent larger because they are limited to that size

&nbsp;

![](/wp-content/uploads/2017/01/8K_IO.png)  
8K I/O Burst Size. Notice how many 8K sectors are read over 4K?

&nbsp;

![](/wp-content/uploads/2017/01/16K.png)  
16K I/O Burst Size


&nbsp;

What I did to compare the differences in performance between all the I/O Burst Size options is I simply tried each size 3 times and took the results as posted by the StatusTray utility for boot time.  The unfortunate thing about the Status Tray is that it's time/throughput calculations are rounded to the second.  This means that the Throughput isn't entirely accurate as a second is a LARGE value when your talking about the difference between 8 to 9 seconds.  If you are just under or over whatever the rounding threshold is it'll change your results when we start getting to these numbers.  But I'll present my results anyways:

<img class="aligncenter size-full wp-image-1919" src="/wp-content/uploads/2017/01/Results.png" alt="" width="492" height="324" srcset="/wp-content/uploads/2017/01/Results.png 492w, /wp-content/uploads/2017/01/Results-300x198.png 300w" sizes="(max-width: 492px) 100vw, 492px" /> 

<span style="text-decoration: underline;">To me, the higher value of I/O Burst Size the better the performance.  </span>

Again, caveats are that I do not know what the impact of a retry is, but if reading from the disk and resending the packet takes ~1ms then I imagine the 'cost' of a retry is very low, even with the larger sizes.  However, if your environment has longer disk reads, high latency, and a poor network with dropped or lost packets then it's possible, I suppose, that higher I/O burst is not for you.

But I hope most PVS environments are something better designed and you actually don't have to worry about it.  

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
