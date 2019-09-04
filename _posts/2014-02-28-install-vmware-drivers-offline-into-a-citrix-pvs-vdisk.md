---
id: 617
title: Install VMWare drivers offline into a Citrix PVS vDisk
date: 2014-02-28T12:38:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/02/28/install-vmware-drivers-offline-into-a-citrix-pvs-vdisk/
permalink: /2014/02/28/install-vmware-drivers-offline-into-a-citrix-pvs-vdisk/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/02/install-vmware-drivers-offline-into.html
blogger_internal:
  - /feeds/7977773/posts/default/4608634143587456589
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - VMWare
  - VMWare tools
---
I am attempting to disable interrupt coalescing for some testing that we are doing with a latency sensitive application and I have 2 VMWare virtual machines configured as such that work as expected.

The latency settings I have done are here:  
<http://www.vmware.com/files/pdf/techpaper/VMW-Tuning-Latency-Sensitive-Workloads.pdf>

Essentially, we turned off interrupt coalescing on the physical NIC by doing the following:  
Logging into the ESXi host with SSH

<pre class="lang:default decode:true "># esxcli network nic list</pre>

(e1000e found as our NIC driver)

<pre class="lang:default decode:true "># esxcli system module parameters list -m e1000e</pre>

(we see InterruptThrottleRate as a parameter)

<pre class="lang:default decode:true "># esxcli system module parameters set -m e1000e -p "InterruptThrottleRate=0"</pre>

<div>
</div>

Then we modified the VMWare virtual machines with these commands:  
To do so through the vSphere Client, go to VM Settings ïƒ Options tab ïƒ Advanced General ïƒ Configuration  
Parameters and add an entry for ethernetX.coalescingScheme with the value of "disabled"

We have 2 NIC's assigned to each of our PVS VM's. Â One NIC is dedicated for the provisioning traffic and one for access to the rest of the network. Â So I had to add 2 lines to my configuration:

<pre class="lang:default decode:true ">ethernet0.coalescingScheme = disabled
ethernet1.coalescingScheme = disabled</pre>

For the VMWare virtual machines we just had the one line:

<pre class="lang:default decode:true ">ethernet0.coalescingScheme = disabled</pre>

Upon powering up the VMWare virtual machines, the per packet latency dropped signficantly and our application was much more responsive.

Unfortunately, even with the settings being identical on the VMWare virtual machines and the Citrix PVS image, the PVS image will not disable interrupt coalescing, consistently showing our packets as have higher latency. Â We built the vDisk image a couple years ago (~2011) and the vDisk now has outdated drivers that I suspect may be the issue. Â The VMWare machines have a VMNET3 driver from August of 2013 and our PVS vDisk has a VMNET3 driver from March 2011.

To test if a newer driver would help, I did not want to reverse image the vDisk image as that is such a pain in the ass. Â So I tried something else. Â I made a new maintenance version of the vDisk and then mounted it on the PVS server:

<pre class="lang:default decode:true ">C:\Users\svc_ctxinstall>"C:\Program Files\Citrix\Provisioning Services\CVhdMount.exe" -p 1 X:\vDisks-&\&65Tn01.14.avhd</pre>

This mounted the vDisk as drive "D:"

I then took the newer driver from the VMWare virtual machine and injected it into the vDisk:

<pre class="lang:default decode:true ">C:\Users\svc_ctxinstall>dism /image:D:\ /add-driver /driver:"C:\Program Files\VMware\VMware Tools\Drivers\vmxnet3"</pre>

I could see my newer driver installed alongside the existing driver:  
<span style="font-family: Courier New, Courier, monospace;"><b>Published Name : oem57.inf</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Original File Name : vmxnet3ndis6.inf</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Inbox : No</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Class Name : Net</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Provider Name : VMware, Inc.</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Date : 08/28/2012</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Version : 1.3.11.0</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><br /> </span><span style="font-family: Courier New, Courier, monospace;">Published Name : oem6.inf</span>  
<span style="font-family: Courier New, Courier, monospace;">Original File Name : vmmouse.inf</span>  
<span style="font-family: Courier New, Courier, monospace;">Inbox : No</span>  
<span style="font-family: Courier New, Courier, monospace;">Class Name : Mouse</span>  
<span style="font-family: Courier New, Courier, monospace;">Provider Name : VMware, Inc.</span>  
<span style="font-family: Courier New, Courier, monospace;">Date : 11/17/2009</span>  
<span style="font-family: Courier New, Courier, monospace;">Version : 12.4.0.6</span>  
<span style="font-family: Courier New, Courier, monospace;"><br /> </span><span style="font-family: Courier New, Courier, monospace;">Published Name : oem7.inf</span>  
<span style="font-family: Courier New, Courier, monospace;">Original File Name : vmaudio.inf</span>  
<span style="font-family: Courier New, Courier, monospace;">Inbox : No</span>  
<span style="font-family: Courier New, Courier, monospace;">Class Name : MEDIA</span>  
<span style="font-family: Courier New, Courier, monospace;">Provider Name : VMware</span>  
<span style="font-family: Courier New, Courier, monospace;">Date : 04/21/2009</span>  
<span style="font-family: Courier New, Courier, monospace;">Version : 5.10.0.3506</span>  
<span style="font-family: Courier New, Courier, monospace;"><br /> </span><span style="font-family: Courier New, Courier, monospace;"><b>Published Name : oem9.inf</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Original File Name : vmxnet3ndis6.inf</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Inbox : No</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Class Name : Net</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Provider Name : VMware, Inc.</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Date : 11/22/2011</b></span>  
<span style="font-family: Courier New, Courier, monospace;"><b>Version : 1.2.24.0</b></span>

Then unmount the vDisk:

<pre class="lang:default decode:true ">C:\Users\svc_ctxinstall>"C:\Program Files\Citrix\Provisioning Services\CVhdMount.exe" -u 1</pre>

I then set the vDisk to maintenance mode, set my PVS target device as maintenance and booted it up. Â When I checked device manager I saw that the driver version was still 1.2.24.0

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-PR4YIvmwVgo/UxDQdBXRVBI/AAAAAAAAAbM/sKRlYaszM1c/s1600/VMNic.png"><img src="http://2.bp.blogspot.com/-PR4YIvmwVgo/UxDQdBXRVBI/AAAAAAAAAbM/sKRlYaszM1c/s1600/VMNic.png" width="285" height="320" border="0" /></a>
</div>

But clicking "Update Driver..." updated our production NIC to the newer version. Â I chose "Search automatically and it found the newer, injected driver. Â I then rebooted the VM and success!

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-K6JTEuvffds/UxDXqfM_dbI/AAAAAAAAAbc/9G7OrOy4Uv0/s1600/VMNic2.png"><img src="http://2.bp.blogspot.com/-K6JTEuvffds/UxDXqfM_dbI/AAAAAAAAAbc/9G7OrOy4Uv0/s1600/VMNic2.png" width="286" height="320" border="0" /></a>
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->