---
id: 2019
title: Citrix PVS Target Device fails with 0x7E
date: 2017-02-15T15:43:36-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2019
permalink: /?p=2019
categories:
  - Uncategorized
---
We were having an issue with a PVS target device failing with 0X7E BSOD

<img class="aligncenter size-full wp-image-2020" src="http://theorypc.ca/wp-content/uploads/2017/02/D9B57F6F.png" alt="" width="681" height="517" srcset="http://theorypc.ca/wp-content/uploads/2017/02/D9B57F6F.png 681w, http://theorypc.ca/wp-content/uploads/2017/02/D9B57F6F-300x228.png 300w" sizes="(max-width: 681px) 100vw, 681px" /> 

So what&#8217;s going on here? Â When PVS boots it does a two-stage boot. Â The first stage is the BNIStack6.sys driver that is &#8216;pre-boot&#8217;. Â This driver connects to the PVS server and starts streaming the image down. Â cvhdmp.sys, is supposed to kick in once plug and play has started so the BNIStack6.sys driver will hand off its duty to this driver. Â In our particular case, this error appeared to be caused by the &#8216;network stack not initializing&#8217;. Â To find out what this meant I reversed imaged the PVS image and attached the hard disk to another target device and booted natively.

I was presented with 2 ghosted NICS (there should have only been 2 as we do multi-homed PVS). Â After a couple mins &#8220;New hardware detected!&#8221; and **2 moreÂ** NIC&#8217;s were installed. Â So you&#8217;d think the simple solution is to uninstall the ghosted NIC&#8217;s and remake the image?

So I did that. Â And I could boot my PVS image!

# BUT!

## And this is a big BUT!.

The only target device that would boot would be the one I &#8216;fixed&#8217; the image. Â Every other target device failed.

I verified our ethernetPCISlot&#8217;s (we are using ESXi) and they were identical between target devices.

So I continued troubleshooting&#8230; Â My first train of thought was I&#8217;ve cleaned up the NIC&#8217;s but it&#8217;s mysterious that they were detected as &#8216;new&#8217;. Â Something must be adding them as &#8216;new&#8217;. Â PVS does rely on some forms of &#8216;non-specificity&#8217; in order to keep itself generalized. Â You can even see this &#8216;generalization&#8217; in device drivers:

<div id="attachment_2021" style="width: 639px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2021" class="wp-image-2021 size-full" src="http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDGeneral.png" width="629" height="364" srcset="http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDGeneral.png 629w, http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDGeneral-300x174.png 300w" sizes="(max-width: 629px) 100vw, 629px" /></p> 
  
  <p id="caption-attachment-2021" class="wp-caption-text">
    Generalized device driver. Any SubSys and Rev will match because those subcategories are *after* the DEV_#### code.
  </p>
</div>

<div id="attachment_2022" style="width: 574px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2022" class="wp-image-2022 size-full" src="http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDSpecific.png" width="564" height="220" srcset="http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDSpecific.png 564w, http://theorypc.ca/wp-content/uploads/2017/02/hardwareIDSpecific-300x117.png 300w" sizes="(max-width: 564px) 100vw, 564px" /></p> 
  
  <p id="caption-attachment-2022" class="wp-caption-text">
    Specific device driver. This driver requires a hardware ID to match these strings to these points, ending in REV_00
  </p>
</div>

&nbsp;

Could it be that the NIC&#8217;s are being tied to specific hardware that exists in the target device and each time we change to a different device it uses a different path upon which the NIC&#8217;s aren&#8217;t connected&#8230;?

Are you following me? Â Â ðŸ˜‰

ToÂ begin my search I looked at the NIC and examined the &#8216;Parent&#8217; property:

<img class="aligncenter size-full wp-image-2023" src="http://theorypc.ca/wp-content/uploads/2017/02/Parent.png" alt="" width="401" height="447" srcset="http://theorypc.ca/wp-content/uploads/2017/02/Parent.png 401w, http://theorypc.ca/wp-content/uploads/2017/02/Parent-269x300.png 269w" sizes="(max-width: 401px) 100vw, 401px" /> 

Fairly long specific string. Â Now let&#8217;s export from Devcon and see how many &#8220;PCI\VEN\_15AD&DEV\_07A0&#8221; devices there are:

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->