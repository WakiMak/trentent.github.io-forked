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
[Continuing on from Part 1](https://theorypc.ca/2016/12/30/lets-make-pvs-target-device-booting-great-again-part-1/), we are looking to optimize the PVS boot process to be as fast as it possibly can be. Â In Part 1 we implemented Jumbo Frames across both the PVS target device and the PVS server and discovered that Jumbo Frames only applies to the portion where BNIStack kicks in.

In this part we are going to examine the option &#8220;I/O burst size (KB)&#8221; Â This policy is explained in the help file:

> I/O burst size â€” The number of bytes that will be transmitted in a single read/write transaction before an ACK is sent from the server or device. The larger the IO burst, the faster the throughput to an individual device, but the more stress placed on the server and network infrastructure. Also, larger IO Bursts increase the likelihood of lost packets and costly retries. Smaller IO bursts reduce single client network throughput, but also reduce server load. Smaller IO bursts also reduce the likelihood of retries. IO Burst Size / MTU size must be <= 32, i.e. only 32 packets can be in a single IO burst before a ACK is needed.

What are these ACK&#8217;s and can we see them? Â We can. Â They are UDP packets sent back from the target device to the PVS server. Â If you open Procmon on the PVS server and startup a target device an ACK looks like so:

<div id="attachment_1902" style="width: 1327px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1902" class="wp-image-1902 size-full" src="http://theorypc.ca/wp-content/uploads/2016/12/48ByteUDPReceieve.png" width="1317" height="920" srcset="http://theorypc.ca/wp-content/uploads/2016/12/48ByteUDPReceieve.png 1317w, http://theorypc.ca/wp-content/uploads/2016/12/48ByteUDPReceieve-300x210.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/48ByteUDPReceieve-768x536.png 768w" sizes="(max-width: 1317px) 100vw, 1317px" /></p> 
  
  <p id="caption-attachment-1902" class="wp-caption-text">
    These highlighted 48byte UDP Receive packets? They are the ACKS
  </p>
</div>

And if we enable the disk view with the network view:

<img class="aligncenter size-full wp-image-1903" src="http://theorypc.ca/wp-content/uploads/2016/12/32KB_HDD_READ.png" alt="" width="1308" height="921" srcset="http://theorypc.ca/wp-content/uploads/2016/12/32KB_HDD_READ.png 1308w, http://theorypc.ca/wp-content/uploads/2016/12/32KB_HDD_READ-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/32KB_HDD_READ-768x541.png 768w" sizes="(max-width: 1308px) 100vw, 1308px" /> 

&nbsp;

With each 32KB read of the hard disk we send out 24 packets, 23 at 1464 bytes and 1 at 440 bytes. Â Add them all together and we get 34,112 Bytes of data. Â This implies an overall overhead of 1344 bytes per sequence of reads or 56 bytes per packet. Â I confirmed it&#8217;s a per-packet overhead by looking at a different read event at a different size:

<img class="aligncenter size-full wp-image-1904" src="http://theorypc.ca/wp-content/uploads/2016/12/DifferentSizedReads.png" alt="" width="1309" height="334" srcset="http://theorypc.ca/wp-content/uploads/2016/12/DifferentSizedReads.png 1309w, http://theorypc.ca/wp-content/uploads/2016/12/DifferentSizedReads-300x77.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/DifferentSizedReads-768x196.png 768w" sizes="(max-width: 1309px) 100vw, 1309px" /> 

If we look at the first read event (8,192) we can see there is 6 packets, 5 at 1464 and one at 1208, totaling 8528 bytes of traffic. Â 8528 &#8211; 8192 = 336 bytes of overhead / 6 packets = 56 bytes.

The same happens with the 16,384Â byte read next in the list. Â 12 packets, 11 at 1464 and one at 952, totaling 17,056. Â 17056 &#8211; 16384 = 672 bytes of overhead / 12 packets = 56 bytes.

So it&#8217;s consistent. Â For every packet at the standard 1506 MTU you are losing 3.8% to overhead. Â But there is secretly more overhead than just that. Â For every read there is a 48 byte ACK overhead on top. Â Admittedly, it&#8217;s not much; but it&#8217;s present.

And how does this look with Jumbo Frames?

<img class="aligncenter size-full wp-image-1905" src="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Overhead.png" alt="" width="1309" height="124" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Overhead.png 1309w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Overhead-300x28.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Overhead-768x73.png 768w" sizes="(max-width: 1309px) 100vw, 1309px" /> 

For a 32KB read we satisfied the request in 4 packets. Â 3 x 8972 bytes and 1 at 6076 bytes totalling 32,992 bytes of transmitted data. Â Subtracting the transmitted data from what is really required 32,992-32,768 = 224 bytes of overhead or&#8230; Â 56 bytes per packet ðŸ™‚

This amounts to a measly 0.6% of overhead when using jumbo frames (an immediate 3% gain!).

But what about this 32KB value. Â What happens if we adjust it longer (or shorter)?

Well, there is a limitation that handicaps us&#8230; Â even if we use Jumbo Frames. Â It is stated here:

> IO Burst Size / MTU size must be <= 32, i.e. <span style="text-decoration: underline;"><strong>only 32 packets can be in a single IO burst</strong></span> before a ACK is needed

_Because Jumbo Frames don&#8217;t occur until after the BNIStack kicks in, we are limited to working out this math at the 1506 MTU size._

The caveat of this is the size isn&#8217;t actually the MTU size of 1506. Â The math is based on the data that fits within, which is 1464Â bytes. Â Doing the math in reverse gives us 1464Â x 32 =Â 45056Â bytes. Â This equals a clear 44K (45056 /1024)Â maximum size. Â Setting IO/Burst to 44KÂ and the target device still boots. Â Counting the packets, there are 32 packets.

<img class="aligncenter size-full wp-image-1913" src="http://theorypc.ca/wp-content/uploads/2017/01/45K.png" alt="" width="1196" height="481" srcset="http://theorypc.ca/wp-content/uploads/2017/01/45K.png 1196w, http://theorypc.ca/wp-content/uploads/2017/01/45K-300x121.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/45K-768x309.png 768w" sizes="(max-width: 1196px) 100vw, 1196px" /> 

So if we up the IO/Burst by 1K to 45K (45*1024 = 46,080 bytes) will it still boot?

[<img class="aligncenter wp-image-1914 size-large" src="http://theorypc.ca/wp-content/uploads/2017/01/No_Boot-1600x774.png" width="1140" height="551" srcset="http://theorypc.ca/wp-content/uploads/2017/01/No_Boot-1600x774.png 1600w, http://theorypc.ca/wp-content/uploads/2017/01/No_Boot-300x145.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/No_Boot-768x372.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />](http://theorypc.ca/wp-content/uploads/2017/01/No_Boot.png)

It does <span style="text-decoration: underline;"><strong>not</strong> </span>boot. Â This enforces a <span style="text-decoration: underline;"><strong>hard limit of 44K</strong> </span>for I/O Burst until the 1st stage supports a larger MTU size. Â I have only explored EFI booting, so I suppose it&#8217;s possible another boot method allows for larger MTU?

The reads themselves are split now, hitting the &#8216;version&#8217; and the &#8216;base&#8217; with the base being 25,600 + 20,480 for the version (46,080 bytes). Â I believe this is normal for versioning though.

So what&#8217;s the recommendation here?

Good question. Â Citrix defaults to 32K I/O Burst Size. Â If we break the operation of the burst size we have 4Â portions:

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

I suspect the 0.4ms difference could be well within a margin of error of my hand based counting. Â I took my numbers from a random sampling of 3 transactions, and averaged. Â I cannot guarantee they were at the same spot of the boot process.

However, it appears the difference between them is close to negligible. Â Question that must be posed is what&#8217;s the cost of a &#8216;retry&#8217; or a missed or faulty UDP packet? Â From the evidence I have it should be fairly small, but I haven&#8217;t figured out a way to test or detect what the turnaround time of a &#8216;retry&#8217; is yet.

Citrix has a utility that gives you some information on what kind of gain youÂ might get. Â It&#8217;s called &#8216;Stream Console&#8217; and it&#8217;s available in the Provisioning Services folder:

&nbsp;

<div id="attachment_1916" style="width: 559px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1916" class="size-full wp-image-1916" src="http://theorypc.ca/wp-content/uploads/2017/01/StreamConsole.png" alt="" width="549" height="531" srcset="http://theorypc.ca/wp-content/uploads/2017/01/StreamConsole.png 549w, http://theorypc.ca/wp-content/uploads/2017/01/StreamConsole-300x290.png 300w" sizes="(max-width: 549px) 100vw, 549px" /></p> 
  
  <p id="caption-attachment-1916" class="wp-caption-text">
    With 4K I/O burst it does not display any packets sent larger because they are limited to that size
  </p>
</div>

&nbsp;

<div id="attachment_1917" style="width: 556px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1917" class="wp-image-1917 size-full" src="http://theorypc.ca/wp-content/uploads/2017/01/8K_IO.png" width="546" height="529" srcset="http://theorypc.ca/wp-content/uploads/2017/01/8K_IO.png 546w, http://theorypc.ca/wp-content/uploads/2017/01/8K_IO-300x291.png 300w" sizes="(max-width: 546px) 100vw, 546px" /></p> 
  
  <p id="caption-attachment-1917" class="wp-caption-text">
    8K I/O Burst Size. Notice how many 8K sectors are read over 4K?
  </p>
</div>

&nbsp;

<div id="attachment_1918" style="width: 557px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1918" class="size-full wp-image-1918" src="http://theorypc.ca/wp-content/uploads/2017/01/16K.png" alt="" width="547" height="531" srcset="http://theorypc.ca/wp-content/uploads/2017/01/16K.png 547w, http://theorypc.ca/wp-content/uploads/2017/01/16K-300x291.png 300w, http://theorypc.ca/wp-content/uploads/2017/01/16K-50x50.png 50w" sizes="(max-width: 547px) 100vw, 547px" /></p> 
  
  <p id="caption-attachment-1918" class="wp-caption-text">
    16K I/O Burst Size
  </p>
</div>

&nbsp;

What I did to compare the differences in performance between all the I/O Burst Size options is I simply tried each size 3 times and took the results as posted by the StatusTray utility for boot time. Â The unfortunate thing about the Status Tray is that it&#8217;s time/throughput calculations are rounded to the second. Â This means that the Throughput isn&#8217;t entirely accurate as a second is a LARGE value when your talking about the difference between 8 to 9 seconds. Â If you are just under or over whatever the rounding threshold is it&#8217;ll change your results when we start getting to these numbers. Â But I&#8217;ll present my results anyways:

<img class="aligncenter size-full wp-image-1919" src="http://theorypc.ca/wp-content/uploads/2017/01/Results.png" alt="" width="492" height="324" srcset="http://theorypc.ca/wp-content/uploads/2017/01/Results.png 492w, http://theorypc.ca/wp-content/uploads/2017/01/Results-300x198.png 300w" sizes="(max-width: 492px) 100vw, 492px" /> 

<span style="text-decoration: underline;">To me, the higher value of I/O Burst Size the better the performance. Â </span>

Again, caveats are that I do not know what the impact of a retry is, but if reading from the disk and resending the packet takes ~1ms then I imagine the &#8216;cost&#8217; of a retry is very low, even with the larger sizes. Â However, if your environment has longer disk reads, high latency, and a poor network with dropped or lost packets then it&#8217;s possible, I suppose, that higher I/O burst is not for you.

But I hopeÂ most PVS environments are something better designed and you actually don&#8217;t have to worry about it. Â ðŸ™‚

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->