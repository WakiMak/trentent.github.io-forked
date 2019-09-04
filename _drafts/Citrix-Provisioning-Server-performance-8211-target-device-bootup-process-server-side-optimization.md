---
id: 1567
title: 'Citrix Provisioning Server performance &#8211; target device bootup process -> server side optimization'
date: 2016-06-29T11:17:36-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1567
permalink: /?p=1567
categories:
  - Blog
---
Continuing from my previous post.

I&#8217;m interested in testing and optimizing Citrix Provisioning Services. Â I&#8217;ve quantified how PVS works on the server side when booting a target device (specifically a Hyper-V VM, OS is 2008 R2). Â I have noticed a few things from the original examination of the performance:

  1. Lots of UDP traffic is generated
  2. Reading data from the vDisk and sending to the target device appeared to have a maximum 32KB &#8216;length&#8217;.

I know &#8220;1&#8221; is a low hanging fruit as one of the easiest changes is modifying MTU size. Â I have modified the MTU on my Hyper-V host to allow for 9K jumbo frames and will modify them on the PVS server and the target device:

<img class="aligncenter size-full wp-image-1568" src="http://theorypc.ca/wp-content/uploads/2016/06/15-2.png" alt="15" width="416" height="461" srcset="http://theorypc.ca/wp-content/uploads/2016/06/15-2.png 416w, http://theorypc.ca/wp-content/uploads/2016/06/15-2-271x300.png 271w" sizes="(max-width: 416px) 100vw, 416px" /> 

&nbsp;

I&#8217;ve enabled Jumbo Packet&#8217;s on the PVS server and the target device at this point and have done no other optimizations. Â It has occurred to me that BDM-type devices are probably made to ensure they work with Hyper-V Gen1 where the NIC&#8217;s need to be &#8216;Legacy&#8217; and don&#8217;t support jumbo frames. Â That&#8217;s probably why Gen2 Hyper-V&#8217;s need to be PXE booted&#8230; Â Anyways, I digress.

Phase 1 boot time was still 11-12 seconds so enabling Jumbo Frames has not made any performance improvements.

Onto Phase 2 ->

<img class="aligncenter size-full wp-image-1569" src="http://theorypc.ca/wp-content/uploads/2016/06/16.png" alt="16" width="453" height="481" srcset="http://theorypc.ca/wp-content/uploads/2016/06/16.png 453w, http://theorypc.ca/wp-content/uploads/2016/06/16-283x300.png 283w" sizes="(max-width: 453px) 100vw, 453px" /> 

Total boot time is now 15 seconds from 16 seconds. Â Probably within the margin of error really.

I now enabled MTU and set it to 9000:

<img class="aligncenter size-full wp-image-1571" src="http://theorypc.ca/wp-content/uploads/2016/06/18.png" alt="18" width="494" height="625" srcset="http://theorypc.ca/wp-content/uploads/2016/06/18.png 494w, http://theorypc.ca/wp-content/uploads/2016/06/18-237x300.png 237w" sizes="(max-width: 494px) 100vw, 494px" /> 

I confirmed this via Procmon. Â You can see the exact moment the UDP switched from MTU 1500 to 9000:

<img class="aligncenter size-full wp-image-1572" src="http://theorypc.ca/wp-content/uploads/2016/06/19.png" alt="19" width="1225" height="628" srcset="http://theorypc.ca/wp-content/uploads/2016/06/19.png 1225w, http://theorypc.ca/wp-content/uploads/2016/06/19-300x154.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/19-768x394.png 768w, http://theorypc.ca/wp-content/uploads/2016/06/19-1024x525.png 1024w" sizes="(max-width: 1225px) 100vw, 1225px" /> 

Since we have a time stamp we can look at what the target device was doing during that time:

<img class="aligncenter size-full wp-image-1573" src="http://theorypc.ca/wp-content/uploads/2016/06/20.png" alt="20" width="633" height="488" srcset="http://theorypc.ca/wp-content/uploads/2016/06/20.png 633w, http://theorypc.ca/wp-content/uploads/2016/06/20-300x231.png 300w" sizes="(max-width: 633px) 100vw, 633px" /> 

So, at the moment that the &#8216;bnistack&#8217; kicks in is when the MTU switches to 9000. Â So this doesn&#8217;t help boot times at all. Â At least with the MTU cutover we can see when the service starts via the procmon on the server side. Â I&#8217;m going to guess that the rest of the values under &#8216;Network&#8217; probably apply to the same point. Â I suspect if there is any benefit to MTU is that it probably reduces CPU load on the PVS server&#8230; Â A test for another day perhaps.

Next, optimizing the client.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->