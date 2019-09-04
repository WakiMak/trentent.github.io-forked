---
id: 1565
title: 'Citrix Provisioning Server target device bootup process &#8211; server side in detail'
date: 2016-06-28T20:19:20-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/06/28/1516-revision-v1/
permalink: /2016/06/28/1516-revision-v1/
---
I had a discussion with someone on the performance of booting up a machine with a PVS image and it occurred to me that I am not overly familiar with it if problems arise. Â So, I decided to dig into it a bit more to try and better understand how PVS boots.

The first thing I have done is loaded a vDisk onto our test Citrix PVS farm. I then rebooted the server to remove any cache that maybe in the system and I started procmon and booted the target device. Â Since we are using a SMB share for our store I filtered on path &#8220;\\&#8221;.

<img class="aligncenter size-full wp-image-1532" src="http://theorypc.ca/wp-content/uploads/2016/06/1-1.png" alt="1" width="516" height="315" srcset="http://theorypc.ca/wp-content/uploads/2016/06/1-1.png 516w, http://theorypc.ca/wp-content/uploads/2016/06/1-1-300x183.png 300w" sizes="(max-width: 516px) 100vw, 516px" /> 

If you set the BDM to show the boot menu:

<img class="aligncenter size-full wp-image-1539" src="http://theorypc.ca/wp-content/uploads/2016/06/8-2.png" alt="8" width="726" height="507" srcset="http://theorypc.ca/wp-content/uploads/2016/06/8-2.png 726w, http://theorypc.ca/wp-content/uploads/2016/06/8-2-300x210.png 300w" sizes="(max-width: 726px) 100vw, 726px" /> 

and start &#8216;procmon-ing&#8217; on the PVS server, the first thing you see is the first MB of data get sent, I suspect this is the MBR and initial boot files. Â It continues to send data at 512 bytes until the initial boot stage is complete.

<img class="aligncenter size-full wp-image-1535" src="http://theorypc.ca/wp-content/uploads/2016/06/4-2.png" alt="4" width="1268" height="463" srcset="http://theorypc.ca/wp-content/uploads/2016/06/4-2.png 1268w, http://theorypc.ca/wp-content/uploads/2016/06/4-2-300x110.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/4-2-768x280.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/4-2-1024x374.png 1024w" sizes="(max-width: 1268px) 100vw, 1268px" /> 

Eventually it changes to a larger length, for the VHD file I&#8217;m seeing 1,422 or 1,082 in Length.

<img class="aligncenter size-full wp-image-1536" src="http://theorypc.ca/wp-content/uploads/2016/06/5-2-e1467054172117.png" alt="5" width="1377" height="602" srcset="http://theorypc.ca/wp-content/uploads/2016/06/5-2-e1467054172117.png 1377w, http://theorypc.ca/wp-content/uploads/2016/06/5-2-e1467054172117-300x131.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/5-2-e1467054172117-768x336.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/5-2-e1467054172117-1024x448.png 1024w" sizes="(max-width: 1377px) 100vw, 1377px" /> 

At this point it continues streaming data until the Windows &#8216;loader&#8217; screen appears. Â It then pauses sending data at this stage (probably because it&#8217;s CPU limited at this stage, activating drivers, etc).

<img class="aligncenter size-full wp-image-1533" src="http://theorypc.ca/wp-content/uploads/2016/06/2-1.png" alt="2" width="644" height="583" srcset="http://theorypc.ca/wp-content/uploads/2016/06/2-1.png 644w, http://theorypc.ca/wp-content/uploads/2016/06/2-1-300x272.png 300w" sizes="(max-width: 644px) 100vw, 644px" /> 

When it hits this pause the StreamProcess.exe pauses at Length 512.

<img class="aligncenter size-full wp-image-1537" src="http://theorypc.ca/wp-content/uploads/2016/06/6-2.png" alt="6" width="1242" height="136" srcset="http://theorypc.ca/wp-content/uploads/2016/06/6-2.png 1242w, http://theorypc.ca/wp-content/uploads/2016/06/6-2-300x33.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/6-2-768x84.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/6-2-1024x112.png 1024w" sizes="(max-width: 1242px) 100vw, 1242px" /> 

With these known points we can see how long the boot process takes to accomplish this task and we can examine what we can do to optimize it.

If we look at the &#8216;File Summary&#8217; we can see from a fresh reboot that just reading the file took 6.29 seconds and sentÂ about 115MB of data.

<img class="aligncenter size-full wp-image-1538" src="http://theorypc.ca/wp-content/uploads/2016/06/7-2.png" alt="7" width="1270" height="125" srcset="http://theorypc.ca/wp-content/uploads/2016/06/7-2.png 1270w, http://theorypc.ca/wp-content/uploads/2016/06/7-2-300x30.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/7-2-768x76.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/7-2-1024x101.png 1024w" sizes="(max-width: 1270px) 100vw, 1270px" /> 

Now Citrix says that if you have sufficient RAM this will be &#8216;cached&#8217; and rebooting will or starting up other target devices will be faster. Â To test that I rebooted my target device to the boot menu, &#8216;cleaned&#8217; the procmon window, started the trace and let it boot.

<img class="aligncenter size-full wp-image-1540" src="http://theorypc.ca/wp-content/uploads/2016/06/9-1.png" alt="9" width="1275" height="126" srcset="http://theorypc.ca/wp-content/uploads/2016/06/9-1.png 1275w, http://theorypc.ca/wp-content/uploads/2016/06/9-1-300x30.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/9-1-768x76.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/9-1-1024x101.png 1024w" sizes="(max-width: 1275px) 100vw, 1275px" /> 

We can see on the reboot of the target device that the total read time the second time was 1.03 seconds. Â So the caching process appears to be working really well and saved us 5 seconds!

<img class="aligncenter size-full wp-image-1541" src="http://theorypc.ca/wp-content/uploads/2016/06/10-1.png" alt="10" width="453" height="467" srcset="http://theorypc.ca/wp-content/uploads/2016/06/10-1.png 453w, http://theorypc.ca/wp-content/uploads/2016/06/10-1-291x300.png 291w" sizes="(max-width: 453px) 100vw, 453px" /> 

Process Monitor shows that the total time it took to get to this part of the boot process was 21 seconds but the boot time of the virtual disk status says 16 seconds. Â So where is it getting this number? Â I believe it&#8217;s starting the timer at the &#8216;marching ants&#8217; boot screen which would be where the BNISTACK gets loaded to when the Citrix PVS Device service starts. Â If you set the Citrix PVS Device service to &#8216;disabled&#8217; or manual and then start the service _after_ you login you can see it calculate the boot time and thus the throughput based on that. Â So the Boot Time is pretty reliant on when the service starts, which if it starts a second or two later than normal your stats will be skewed somewhat.

<div id="attachment_1542" style="width: 1262px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1542" class="wp-image-1542 size-full" src="http://theorypc.ca/wp-content/uploads/2016/06/11-1.png" alt="11" width="1252" height="481" srcset="http://theorypc.ca/wp-content/uploads/2016/06/11-1.png 1252w, http://theorypc.ca/wp-content/uploads/2016/06/11-1-300x115.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/11-1-768x295.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/11-1-1024x393.png 1024w" sizes="(max-width: 1252px) 100vw, 1252px" /></p> 
  
  <p id="caption-attachment-1542" class="wp-caption-text">
    8 second &#8220;breather&#8221; at 29:56 to 30:04
  </p>
</div>

So once the marching ants start and the &#8216;streamprocess.exe&#8217; takes a breather then the StreamProcess.exe starts some new threads and continues streaming data.

The pause is around 8 seconds.

<img class="aligncenter size-full wp-image-1544" src="http://theorypc.ca/wp-content/uploads/2016/06/13-1.png" alt="13" width="713" height="595" srcset="http://theorypc.ca/wp-content/uploads/2016/06/13-1.png 713w, http://theorypc.ca/wp-content/uploads/2016/06/13-1-300x250.png 300w" sizes="(max-width: 713px) 100vw, 713px" /> 

We can see the boot time is listed as 4:29:57 (actually 3:29:57 but our timezone is off on this vDisk).

The service finishes starting at 4:30:17.

<img class="aligncenter size-full wp-image-1543" src="http://theorypc.ca/wp-content/uploads/2016/06/12-1.png" alt="12" width="710" height="598" srcset="http://theorypc.ca/wp-content/uploads/2016/06/12-1.png 710w, http://theorypc.ca/wp-content/uploads/2016/06/12-1-300x253.png 300w" sizes="(max-width: 710px) 100vw, 710px" /> 

So the &#8217;16&#8217; seconds the virtual status reports? Â It starts counting when the &#8216;bnistack&#8217; starts (4:30:01 &#8211; 4:30:17)<img class="aligncenter size-full wp-image-1545" src="http://theorypc.ca/wp-content/uploads/2016/06/14-1.png" alt="14" width="713" height="600" srcset="http://theorypc.ca/wp-content/uploads/2016/06/14-1.png 713w, http://theorypc.ca/wp-content/uploads/2016/06/14-1-300x252.png 300w" sizes="(max-width: 713px) 100vw, 713px" />

So what can we do to make this faster? Â At this point I had to switch to my home lab which runs PVS 7.9 but everything seems to be the same as above (PVS 7.7).

The first thing I want to do is remove the hard disk from being a factor. Â I have 3 storage mediums, a NAS, my local SSD and I created a RAMDisk of 30GB in size. Â I tested each one. Â First the RAM Disk:

<div id="attachment_1547" style="width: 1034px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1547" class="wp-image-1547 size-large" src="http://theorypc.ca/wp-content/uploads/2016/06/diskspd-1024x579.png" alt="diskspd" width="1024" height="579" srcset="http://theorypc.ca/wp-content/uploads/2016/06/diskspd-1024x579.png 1024w, http://theorypc.ca/wp-content/uploads/2016/06/diskspd-300x170.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/diskspd-768x434.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/diskspd.png 1057w" sizes="(max-width: 1024px) 100vw, 1024px" /></p> 
  
  <p id="caption-attachment-1547" class="wp-caption-text">
    Performance on the RAMDisk looks really, really good.
  </p>
</div>

SSD:

<div id="attachment_1548" style="width: 1067px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1548" class="size-full wp-image-1548" src="http://theorypc.ca/wp-content/uploads/2016/06/SSD.png" alt="Performance is still pretty good" width="1057" height="597" srcset="http://theorypc.ca/wp-content/uploads/2016/06/SSD.png 1057w, http://theorypc.ca/wp-content/uploads/2016/06/SSD-300x169.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/SSD-768x434.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/SSD-1024x578.png 1024w" sizes="(max-width: 1057px) 100vw, 1057px" /></p> 
  
  <p id="caption-attachment-1548" class="wp-caption-text">
    Performance is still pretty good
  </p>
</div>

And lastly my NAS (connected via iSCSI):

<img class="aligncenter size-full wp-image-1549" src="http://theorypc.ca/wp-content/uploads/2016/06/NAS.png" alt="NAS" width="1055" height="596" srcset="http://theorypc.ca/wp-content/uploads/2016/06/NAS.png 1055w, http://theorypc.ca/wp-content/uploads/2016/06/NAS-300x169.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/NAS-768x434.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/NAS-1024x578.png 1024w" sizes="(max-width: 1055px) 100vw, 1055px" /> 

The RAMDisk (unsurprisingly) crushes it for performance. Â I decided to set my PVSStore toÂ use the RAMÂ diskÂ for vDisk images.

At this point, I started wondering, what&#8217;s my goal? Â My goal is to determine if MTU size can make a difference and what other tweaks can be done on the network to speed up the performance of the target devices. Â With that I have the following info. Â I have configured my PVS server to have 4 vCPU. Â It has 48GB of RAM with 22GB devoted to a RAM drive hosting one image. Â I have modified the Citrix [network values according to the new Citrix standard](https://www.citrix.com/blogs/2016/03/30/updated-guidance-on-pvs-ports-and-threads/). Â That standard is 1 thread per vCPU (4 threads per port in total) with my port range being 55 ports (6910-6968 &#8211; 3 ports are reserved)

<img class="aligncenter size-full wp-image-1550" src="http://theorypc.ca/wp-content/uploads/2016/06/1-2.png" alt="1" width="498" height="625" srcset="http://theorypc.ca/wp-content/uploads/2016/06/1-2.png 498w, http://theorypc.ca/wp-content/uploads/2016/06/1-2-239x300.png 239w" sizes="(max-width: 498px) 100vw, 498px" /><img class="aligncenter size-full wp-image-1551" src="http://theorypc.ca/wp-content/uploads/2016/06/2-2.png" alt="2" width="512" height="483" srcset="http://theorypc.ca/wp-content/uploads/2016/06/2-2.png 512w, http://theorypc.ca/wp-content/uploads/2016/06/2-2-300x283.png 300w" sizes="(max-width: 512px) 100vw, 512px" /> 

My plan is to look at the booting of our vDisks in two different phases. Â Phase 1 is the &#8216;Boot to Marching Ants&#8217; which is very noticeable in the traces for my setup phase 1 only uses the legacy NIC of my HyperV system so it&#8217;s capped at 100MB. Â Phase 2 is the part where the BNIStack kicks in and is the part measured by the Citrix PVS Device service.

So now that we&#8217;ve established our store, I ran several boots to get theÂ baseline. Â I was curious to know how much of an impact a RAMDisk may have on <span style="text-decoration: underline;">first</span> boot so I traced it:

<img class="aligncenter size-full wp-image-1552" src="http://theorypc.ca/wp-content/uploads/2016/06/3-3.png" alt="3" width="928" height="133" srcset="http://theorypc.ca/wp-content/uploads/2016/06/3-3.png 928w, http://theorypc.ca/wp-content/uploads/2016/06/3-3-300x43.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/3-3-768x110.png 768w" sizes="(max-width: 928px) 100vw, 928px" /> 

0.22 seconds spent reading the boot file. Â Pretty fast in terms of accessing the vDisk but total boot time to &#8216;phase 1&#8242; was &#8217;12&#8217; seconds:

<img class="aligncenter size-full wp-image-1553" src="http://theorypc.ca/wp-content/uploads/2016/06/4-3.png" alt="4" width="1244" height="878" srcset="http://theorypc.ca/wp-content/uploads/2016/06/4-3.png 1244w, http://theorypc.ca/wp-content/uploads/2016/06/4-3-300x212.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/4-3-768x542.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/4-3-1024x723.png 1024w" sizes="(max-width: 1244px) 100vw, 1244px" /> 

3:16:41->53.

Network activity looked like so:

<img class="aligncenter size-full wp-image-1554" src="http://theorypc.ca/wp-content/uploads/2016/06/5-3.png" alt="5" width="1245" height="1097" srcset="http://theorypc.ca/wp-content/uploads/2016/06/5-3.png 1245w, http://theorypc.ca/wp-content/uploads/2016/06/5-3-300x264.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/5-3-768x677.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/5-3-1024x902.png 1024w" sizes="(max-width: 1245px) 100vw, 1245px" /> 

Lots and lots of sends and receive at 1464B (MTU 1506 &#8211; 42 bytes for UDP header).

Lastly, a graph showing the process activity during the boot process:

<img class="aligncenter size-full wp-image-1555" src="http://theorypc.ca/wp-content/uploads/2016/06/proc_activity.png" alt="proc_activity" width="1600" height="855" srcset="http://theorypc.ca/wp-content/uploads/2016/06/proc_activity.png 1600w, http://theorypc.ca/wp-content/uploads/2016/06/proc_activity-300x160.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/proc_activity-768x410.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/proc_activity-1024x547.png 1024w" sizes="(max-width: 1600px) 100vw, 1600px" /> 

Phase 2 reported that it took 16 seconds:

<img class="aligncenter size-full wp-image-1556" src="http://theorypc.ca/wp-content/uploads/2016/06/6-3.png" alt="6" width="453" height="480" srcset="http://theorypc.ca/wp-content/uploads/2016/06/6-3.png 453w, http://theorypc.ca/wp-content/uploads/2016/06/6-3-283x300.png 283w" sizes="(max-width: 453px) 100vw, 453px" /> 

Looking at the event logs it appears, at least with a Gen1 machine, that there is a &#8216;hand-off&#8217; of the Legacy NIC to the Synthetic NIC . Â This process seems to consume about 5 seconds of time. Â This screenshot shows when the system registered as &#8216;powered on&#8217; (EventID 12), where it started the synthetic NIC (all of the Hyper-V-Netvsc) to where the BNIStack then kicksÂ in.

<img class="aligncenter size-full wp-image-1557" src="http://theorypc.ca/wp-content/uploads/2016/06/7-3.png" alt="7" width="631" height="385" srcset="http://theorypc.ca/wp-content/uploads/2016/06/7-3.png 631w, http://theorypc.ca/wp-content/uploads/2016/06/7-3-300x183.png 300w" sizes="(max-width: 631px) 100vw, 631px" /> 

During this entire time there is NO network activity from the PVS server. Â So this is a set of events that may or may not be CPU dependent, but is definitely local to your target device. Â The PVS server didn&#8217;t report activity until 3:35:29 which coincides to the &#8216;Kernel-Processor-Power&#8217; event.

<img class="aligncenter size-full wp-image-1558" src="http://theorypc.ca/wp-content/uploads/2016/06/8-3.png" alt="8" width="756" height="339" srcset="http://theorypc.ca/wp-content/uploads/2016/06/8-3.png 756w, http://theorypc.ca/wp-content/uploads/2016/06/8-3-300x135.png 300w" sizes="(max-width: 756px) 100vw, 756px" /> 

Technically, to theÂ stats collected by the Citrix PVS Device service starts counting right at boot (EventID12) so these delays do add to the total &#8216;boot&#8217; time.

TheÂ 4-5 second delay from bnistack to &#8216;Kernel-Processor-Power&#8217; adds to the total. Â It does look like the &#8216;crash dump driver&#8217; may add a second or so to the total startup that may need to see if we can disable.

<img class="aligncenter size-full wp-image-1559" src="http://theorypc.ca/wp-content/uploads/2016/06/9-2.png" alt="9" width="599" height="249" srcset="http://theorypc.ca/wp-content/uploads/2016/06/9-2.png 599w, http://theorypc.ca/wp-content/uploads/2016/06/9-2-300x125.png 300w" sizes="(max-width: 599px) 100vw, 599px" /> 

&nbsp;

The boot process then &#8216;ends&#8217; when the service is started, or, perhaps, at one of the bnistack events (10 or 160) directly preceding it)

<img class="aligncenter size-full wp-image-1560" src="http://theorypc.ca/wp-content/uploads/2016/06/11-2.png" alt="11" width="623" height="424" srcset="http://theorypc.ca/wp-content/uploads/2016/06/11-2.png 623w, http://theorypc.ca/wp-content/uploads/2016/06/11-2-300x204.png 300w" sizes="(max-width: 623px) 100vw, 623px" /> 

The graph of Phase 2 looks like so:

<img class="aligncenter size-full wp-image-1561" src="http://theorypc.ca/wp-content/uploads/2016/06/12-2.png" alt="12" width="1598" height="848" srcset="http://theorypc.ca/wp-content/uploads/2016/06/12-2.png 1598w, http://theorypc.ca/wp-content/uploads/2016/06/12-2-300x159.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/12-2-768x408.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/12-2-1024x543.png 1024w" sizes="(max-width: 1598px) 100vw, 1598px" /> 

We can also see that &#8216;Phase 2&#8217; has a much more different look to it then &#8216;Phase 1&#8217; when we are examining the network:

<img class="aligncenter size-full wp-image-1562" src="http://theorypc.ca/wp-content/uploads/2016/06/13-2.png" alt="13" width="1251" height="1039" srcset="http://theorypc.ca/wp-content/uploads/2016/06/13-2.png 1251w, http://theorypc.ca/wp-content/uploads/2016/06/13-2-300x249.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/13-2-768x638.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/13-2-1024x850.png 1024w" sizes="(max-width: 1251px) 100vw, 1251px" /> 

Each time a chunk of data is read that data is sent to the server in the numerous UDP packets as required. Â Phase 1 seemed to require an acknowledgement for each packet, Phase 2 is much more efficent in this regard. Â There are also numerous points throughout the boot process where it&#8217;s reading data in 32KB chunks and sending that down the wire:

<img class="aligncenter size-full wp-image-1563" src="http://theorypc.ca/wp-content/uploads/2016/06/14-2.png" alt="14" width="1607" height="1000" srcset="http://theorypc.ca/wp-content/uploads/2016/06/14-2.png 1607w, http://theorypc.ca/wp-content/uploads/2016/06/14-2-300x187.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/14-2-768x478.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/14-2-1024x637.png 1024w" sizes="(max-width: 1607px) 100vw, 1607px" /> 

Now that I have some visibility on the PVS server side on how it operates and interacts with the target device I&#8217;m going to delve into modifying the properties of the server to see what can be done to optimize it.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->