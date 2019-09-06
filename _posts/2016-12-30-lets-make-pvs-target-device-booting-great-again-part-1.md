---
id: 1874
title: Lets Make PVS Target Device Booting Great Again (Part 1)
date: 2016-12-30T16:53:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1874
permalink: /2016/12/30/lets-make-pvs-target-device-booting-great-again-part-1/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2016/12/Booted.mp4
    180
    video/mp4
    
image: /wp-content/uploads/2016/12/UDPSend.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - Provisioning Services
  - PVS
  - Target Device
  - Windows 10
---
Some discussions have swirled recently about implementing VDI. Â One of the challenges with VDI are things like slow boot times necessitating having machines pre-powered on, requiring a pool of machines sitting using server resources until a logon request comes in and more machines are powered on to meet the demand... Â But what if your boot time is measured in the seconds? Â Something so low you could keep the 'pool' of machines sitting on standby to 1 or 2 or even none!

I'm interested in investigating if this is possible. Â I previously looked at this as a curiosity and achieved some good results:

<img class="aligncenter size-full wp-image-1875" src="http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Time.jpg" alt="" width="814" height="862" srcset="http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Time.jpg 814w, http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Time-283x300.jpg 283w, http://theorypc.ca/wp-content/uploads/2016/12/PVS_Boot_Time-768x813.jpg 768w" sizes="(max-width: 814px) 100vw, 814px" /> 

&nbsp;

However, that was a non-domain Server 2012 R2 fresh out of the box. Â I tweaked my infrastructure a bit by storing the vDisk on a RAM Disk with Jumbo Frames (9k) to supercharge it somewhat.

Today, I'm going to investigate this again with PVS 7.12, UEFI, Windows 10, on a domain. Â I'll show how I investigated booting performance and see what we can do to improve it.

The first thing I'm going to do is install Windows 10, join it to the domain and create a vDisk.

<img class="aligncenter size-full wp-image-1876" src="http://theorypc.ca/wp-content/uploads/2016/12/Win10_vDisk.png" alt="" width="522" height="288" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Win10_vDisk.png 522w, http://theorypc.ca/wp-content/uploads/2016/12/Win10_vDisk-300x166.png 300w" sizes="(max-width: 522px) 100vw, 522px" /> 

Done. Â Because I don't have SCVMM setup on my home lab I had to muck my way to enabling UEFI HDD boot. Â I went into the PVS folder (C:\ProgramData\Citrix\Provisioning Services) and copied out the BDMTemplate_uefi.vhd to my Hyper-V target Device folder

<img class="aligncenter size-full wp-image-1877" src="http://theorypc.ca/wp-content/uploads/2016/12/UEFIHDD.png" alt="" width="776" height="337" srcset="http://theorypc.ca/wp-content/uploads/2016/12/UEFIHDD.png 776w, http://theorypc.ca/wp-content/uploads/2016/12/UEFIHDD-300x130.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/UEFIHDD-768x334.png 768w" sizes="(max-width: 776px) 100vw, 776px" /> 

I then edited my Hyper-V Target Device (Gen2) and added the VHD:

<img class="aligncenter size-full wp-image-1878" src="http://theorypc.ca/wp-content/uploads/2016/12/HyperV_Settings.png" alt="" width="725" height="683" srcset="http://theorypc.ca/wp-content/uploads/2016/12/HyperV_Settings.png 725w, http://theorypc.ca/wp-content/uploads/2016/12/HyperV_Settings-300x283.png 300w" sizes="(max-width: 725px) 100vw, 725px" /> 

I then mounted the VHD and modified the PVSBOOT.INI file so it pointed to my PVS server:

<img class="aligncenter size-full wp-image-1879" src="http://theorypc.ca/wp-content/uploads/2016/12/UEFI_HDD_IMG.png" alt="" width="948" height="619" srcset="http://theorypc.ca/wp-content/uploads/2016/12/UEFI_HDD_IMG.png 948w, http://theorypc.ca/wp-content/uploads/2016/12/UEFI_HDD_IMG-300x196.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/UEFI_HDD_IMG-768x501.png 768w" sizes="(max-width: 948px) 100vw, 948px" /> 

&nbsp;

&nbsp;

I then created my target device in the PVS console:

<img class="aligncenter size-full wp-image-1880" src="http://theorypc.ca/wp-content/uploads/2016/12/Target_Device.png" alt="" width="482" height="431" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Target_Device.png 482w, http://theorypc.ca/wp-content/uploads/2016/12/Target_Device-300x268.png 300w" sizes="(max-width: 482px) 100vw, 482px" /> 

&nbsp;

And Viola! Â It Booted.

<div style="width: 1024px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1874-14" width="1024" height="872" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/12/Booted.mp4?_=14" /><a href="http://theorypc.ca/wp-content/uploads/2016/12/Booted.mp4">http://theorypc.ca/wp-content/uploads/2016/12/Booted.mp4</a></video>
</div>

&nbsp;

<img class="aligncenter size-full wp-image-1882" src="http://theorypc.ca/wp-content/uploads/2016/12/8sBootTime.png" alt="" width="452" height="484" srcset="http://theorypc.ca/wp-content/uploads/2016/12/8sBootTime.png 452w, http://theorypc.ca/wp-content/uploads/2016/12/8sBootTime-280x300.png 280w" sizes="(max-width: 452px) 100vw, 452px" /> 

And out of the gate we are getting 8 second boot times. Â At this point I don't have it set with a RAM drive or anything so this is pretty stock, albeit on really fast hardware. Â My throughput is crushing my previous speed record, so if I can reduce the amount of bytes read (it's literally bytes read/time = throughput) I can improve the speed of my boot time. Â On the flip side, I can try to increase my throughput but that's a bit harder.

However, there are some tricks I can try.

I have Jumbo Frames enabled across my network. Â At this stage I do not have them set but we can enable them to see if it helps.

To verify their operation I'm going to trace the boot operation from the PVS server using procmon:

<img class="aligncenter size-full wp-image-1883" src="http://theorypc.ca/wp-content/uploads/2016/12/UDP.png" alt="" width="962" height="800" srcset="http://theorypc.ca/wp-content/uploads/2016/12/UDP.png 962w, http://theorypc.ca/wp-content/uploads/2016/12/UDP-300x249.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/UDP-768x639.png 768w" sizes="(max-width: 962px) 100vw, 962px" /> 

We can clearly see the UDP packet size is capping out at 1464 bytes, making itÂ 1464+Â **8 byte** UDP header +Â **20 byte** IP header = 1492 bytes. Â I enabled Jumbo Frames

<img class="aligncenter size-full wp-image-1884" src="http://theorypc.ca/wp-content/uploads/2016/12/JumboHyperV.png" alt="" width="404" height="456" srcset="http://theorypc.ca/wp-content/uploads/2016/12/JumboHyperV.png 404w, http://theorypc.ca/wp-content/uploads/2016/12/JumboHyperV-266x300.png 266w" sizes="(max-width: 404px) 100vw, 404px" /> 

Under Server Properties in the PVS console I adjusted the MTU to match the NIC:

<img class="aligncenter size-full wp-image-1885" src="http://theorypc.ca/wp-content/uploads/2016/12/PVS_MTU.png" alt="" width="552" height="563" srcset="http://theorypc.ca/wp-content/uploads/2016/12/PVS_MTU.png 552w, http://theorypc.ca/wp-content/uploads/2016/12/PVS_MTU-294x300.png 294w, http://theorypc.ca/wp-content/uploads/2016/12/PVS_MTU-50x50.png 50w" sizes="(max-width: 552px) 100vw, 552px" /> 

&nbsp;

You then need to restart the PVS services for it take effect.

<img class="aligncenter size-full wp-image-1886" src="http://theorypc.ca/wp-content/uploads/2016/12/restart_service.png" alt="" width="345" height="186" srcset="http://theorypc.ca/wp-content/uploads/2016/12/restart_service.png 345w, http://theorypc.ca/wp-content/uploads/2016/12/restart_service-300x162.png 300w" sizes="(max-width: 345px) 100vw, 345px" /> 

I then made a new vDisk version and enabled Jumbo Frames in the OS of the target device. Â I did a quick ping test to validate that Jumbo Frames are passing correctly.

<img class="aligncenter size-full wp-image-1887" src="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_working.png" alt="" width="771" height="702" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_working.png 771w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_working-300x273.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_working-768x699.png 768w" sizes="(max-width: 771px) 100vw, 771px" /> 

I then did started procmon on the PVS server, set the target device to boot...

and...

<img class="aligncenter size-full wp-image-1888" src="http://theorypc.ca/wp-content/uploads/2016/12/1464.png" alt="" width="1025" height="335" srcset="http://theorypc.ca/wp-content/uploads/2016/12/1464.png 1025w, http://theorypc.ca/wp-content/uploads/2016/12/1464-300x98.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/1464-768x251.png 768w" sizes="(max-width: 1025px) 100vw, 1025px" /> 

&nbsp;

1464 sized UDP packets. Â A little smaller than the 9000 bytes or so it's supposed to be. Â Scrolling down a little futher, however, shows:

<img class="aligncenter size-full wp-image-1889" src="http://theorypc.ca/wp-content/uploads/2016/12/JumboUDP.png" alt="" width="1151" height="105" srcset="http://theorypc.ca/wp-content/uploads/2016/12/JumboUDP.png 1151w, http://theorypc.ca/wp-content/uploads/2016/12/JumboUDP-300x27.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/JumboUDP-768x70.png 768w" sizes="(max-width: 1151px) 100vw, 1151px" /> 

&nbsp;

Notice the amount of UDP packets sent in the smaller frame size?

<img class="aligncenter size-full wp-image-1890" src="http://theorypc.ca/wp-content/uploads/2016/12/UDP_Read_Write_Pattern.png" alt="" width="1141" height="891" srcset="http://theorypc.ca/wp-content/uploads/2016/12/UDP_Read_Write_Pattern.png 1141w, http://theorypc.ca/wp-content/uploads/2016/12/UDP_Read_Write_Pattern-300x234.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/UDP_Read_Write_Pattern-768x600.png 768w" sizes="(max-width: 1141px) 100vw, 1141px" /> 

&nbsp;

Approximately 24 packets until it gets a "Receive" notification to send the next batch of packets. Â These 24 packets account for ~34,112 bytes of data per sequence. Â Total time for each batch of packets is 4-6ms.

If we follow through to when the jumbo frames kick in we see the following:

<img class="aligncenter size-full wp-image-1891" src="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Pattern.png" alt="" width="1140" height="541" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Pattern.png 1140w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Pattern-300x142.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Pattern-768x364.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

This is a bit harder to read because the MIO (Multiple Input Output) kicks in here and so there are actually two threads executing the read operations as opposed to the single thread above.

Regardless, I think I've hit on a portion that is executing more-or-less sequentially. Â The total amount of data being passed in these sequences is ~32,992 bytes but the time to execute on them is 1-2ms! Â We have essentially doubled the performance of ourÂ _latency_ on our hard disk.

So why is the data being sent like this? Â Again, procmon brings some visibility here:

<img class="aligncenter size-full wp-image-1892" src="http://theorypc.ca/wp-content/uploads/2016/12/StreamProcess.png" alt="" width="1150" height="589" srcset="http://theorypc.ca/wp-content/uploads/2016/12/StreamProcess.png 1150w, http://theorypc.ca/wp-content/uploads/2016/12/StreamProcess-300x154.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/StreamProcess-768x393.png 768w" sizes="(max-width: 1150px) 100vw, 1150px" /> 

Each "UDP Receieve" packet is a validation that the data it received was good and instructs the Sream Process to read and send the next portion of the file on the disk. Â If we move to the jumbo frame portion of the boot process we can see IO goes all over the place in size and where the reads are to occur:

<img class="aligncenter size-full wp-image-1893" src="http://theorypc.ca/wp-content/uploads/2016/12/Read_Requests.png" alt="" width="1138" height="804" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Read_Requests.png 1138w, http://theorypc.ca/wp-content/uploads/2016/12/Read_Requests-300x212.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Read_Requests-768x543.png 768w" sizes="(max-width: 1138px) 100vw, 1138px" /> 

So, again, jumbo frames are a big help here as all requests under 8K can be serviced in 1 packet, and there are usually MORE requests under 8K then above. Â Fortunately, Procmon can give us some numbers to illustrate this. Â I started and stopped the procmon trace for each run of a Network Boot with Jumbo Frames and without:

<div id="attachment_1894" style="width: 953px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1894" class="size-full wp-image-1894" src="http://theorypc.ca/wp-content/uploads/2016/12/Regular_Packet.png" alt="" width="943" height="472" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Regular_Packet.png 943w, http://theorypc.ca/wp-content/uploads/2016/12/Regular_Packet-300x150.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Regular_Packet-768x384.png 768w" sizes="(max-width: 943px) 100vw, 943px" /></p> 
  
  <p id="caption-attachment-1894" class="wp-caption-text">
    Standard MTU (1506)
  </p>
</div>

&nbsp;

<div id="attachment_1895" style="width: 956px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1895" class="size-full wp-image-1895" src="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_AmountOfNetworkTraffic.png" alt="" width="946" height="471" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_AmountOfNetworkTraffic.png 946w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_AmountOfNetworkTraffic-300x149.png 300w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Frames_AmountOfNetworkTraffic-768x382.png 768w" sizes="(max-width: 946px) 100vw, 946px" /></p> 
  
  <p id="caption-attachment-1895" class="wp-caption-text">
    Jumbo Frame MTU (9014)
  </p>
</div>

&nbsp;

The number we are really after is the 192.168.1.88:6905. Â The total number of events are solidly in half with the number of sends about a 1/3 less! Â It was fast enough that it was able to process double the amount of data in Bytes sent to the target device and bytes received from the target device!

Does this help our throughput? Â Yes, it does:

<img class="aligncenter size-full wp-image-1896" src="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Boot.png" alt="" width="450" height="485" srcset="http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Boot.png 450w, http://theorypc.ca/wp-content/uploads/2016/12/Jumbo_Boot-278x300.png 278w" sizes="(max-width: 450px) 100vw, 450px" /> 

&nbsp;

"But Trentent! Â That doesn't show the massive gains you are spewing! Â It's only 4MB/s more in Through-put!"

And you are correct. Â So why aren't we seeing more gains? Â The issue lies with how PVS boots. Â It boots in two stages. Â If you are familiar with PVS on Hyper-V from a year ago or more you are probably more aware of this issue. Â Essentially, PVS breaks the boot into the first stage (bootloader stage) which starts in, essentially, a lower-performance mode (standard MTU). Â Once the BNIStack loads it kicks into Jumbo Packet mode with the loading of the Synthetic NIC driver. Â The benefits from Jumbo Frames doesn't occur until this stage. Â So when does Jumbo Frames kick in? Â You can see it in Event Viewer.

<img class="aligncenter size-full wp-image-1897" src="http://theorypc.ca/wp-content/uploads/2016/12/First_Stage_Boot.png" alt="" width="641" height="461" srcset="http://theorypc.ca/wp-content/uploads/2016/12/First_Stage_Boot.png 641w, http://theorypc.ca/wp-content/uploads/2016/12/First_Stage_Boot-300x216.png 300w" sizes="(max-width: 641px) 100vw, 641px" /> 

From everything I see with Procmon, first stage boot ends on that first Ntfs event. Â So out of the original 8 seconds, 4 is spent on first stage boot where Jumbo Packets are not enabled. Â Everything after there is impacted (positively). Â So for our 4 seconds "standard MTU" boot, bringing that down by a second is a 25% improvement! Â Not small potatoes.

I intend to do more investigation into what I can do to improve boot performance for PVS target devices so stay tuned! Â ðŸ™‚

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->