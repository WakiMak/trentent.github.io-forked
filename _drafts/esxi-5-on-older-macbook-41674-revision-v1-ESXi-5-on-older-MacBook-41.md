---
id: 1431
title: ESXi 5 on older MacBook 4,1
date: 2016-04-28T23:44:57-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/674-revision-v1/
permalink: /2016/04/28/674-revision-v1/
---
I&#8217;m looking to get my MacBook 4,1 under a little more utilization and I thought playing with VMWare&#8217;s ESXi-5 on it would be interesting. &nbsp;As well, I&#8217;m hoping that I&#8217;ll be able to install a virtualized Mountain Lion in there (unsupported natively on this MacBook). &nbsp;My first attempt at installing ESXi 5U1 was a miserable disaster. &nbsp;ESXi won&#8217;t let you complete the installation process without a NIC. &nbsp;This triggered a search to find what NIC was in my MacBook (Marvell Yukon 88E8055 VEN\_11AB&DEV\_436A) and a requirement to get it working.

I found this thread:  
<http://www.vm-help.com/forum/viewtopic.php?f=25&t=3558&start=10>

It got me started on the path I needed to enable the drivers for my MacBook. &nbsp;I found that the Marvell driver is already included but it is only enabled for two device ID&#8217;s and neither were the same as my macbook&#8217;s; but they were from the same family. 

<http://www.vm-help.com/esx41/sky2_driver.php>

I later found that the driver supports my device ID but you need to edit a text file in the VMWare driver files to enable it. &nbsp;This will not work though, because VMWare signs their files and if there is a mismatch it will error out (unable to boot or corrupt image or some such). &nbsp;To get around this we need to inject the driver into the ESXi installer.

In order to do this I needed to understand how to make a driver injectable. &nbsp;It appears whatever you make will overwrite files during the unpacking process when ESXi is booting. &nbsp;I was able to &#8220;sort-of&#8221; confirm this by packing up the sky2 driver and including my Device ID in the ESXi driver ID file. &nbsp;When I packed up the sky2 binary driver in addition to the text files the install would error out. &nbsp;Since it&#8217;s redundant to include the driver as well, I just tried including the driver text file that enables the deviceID. &nbsp;This file could be located here:

&#8220;etcvmwaredriver.map.dsky2.map&#8221;

To also make this more complicated I did everything on Windows and had to install UnxUtils and UnxUpdates and some Cygwin tools to do the archiving/unarchiving.

By editing sky2.map I could add my device ID:

regtype=linux,bus=pci,id=11ab:4354 0000:0000,driver=sky2,class=network  
regtype=linux,bus=pci,id=11ab:4362 0000:0000,driver=sky2,class=network  
**regtype=linux,bus=pci,id=11ab:436A 0000:0000,driver=sky2,class=network**

I compressed the file into a .tgz (bsdtar &nbsp;-cvzf sky2.tgz &#8220;etc&#8221;) and then used ESXi Customizer 2.7 to inject it into a generic ESXi 5.0U1 image. &nbsp;I then used Linux &nbsp;Live USB Creator to move the ISO to a USB key that was made bootable.

I could now boot my MacBook4,1 off the USB key. &nbsp;At least, it worked to a point. &nbsp;I got to a stage where I was notified that I had no Network Adapters Found. &nbsp;At this point I dropped into the ESXi console (Fn-Option-F1), logged in to root and ran the following commands:

vmkload_mod sky2  
#loads the sky2 driver  
lspci -p  
#confirms sky2 driver is loaded for 436A DEV ID  
esxcfg-init -n  
#initializes network for default settings  
installerhelper.sh  
#preps install command  
install

The install command kicked me out to the default console where I was sitting at the &#8220;Network Adapters Not Found&#8221; screen. &nbsp;Simply Fn-Option-F1 to return to the console and run through the install prompts. &nbsp;Choose the same USB key you used earlier (or choose a new one if you prefer) and do the install. &nbsp;Once it&#8217;s complete; restart onto the USB key. &nbsp;You should go into a full ESXi install but the network (for me anyways) was still no working correctly. &nbsp;I had to enable the console through the &#8220;GUI&#8221; of the text screen, drop into the console and run **vmkload_mod sky2** and I had network again and was up and running. &nbsp;Once I can figure out how to automatically run that command I will come back and update this post. &nbsp;But for now I have ESXi working on a old MacBook and hopefully I&#8217;ll be able to install Mountain Lion on this old workstation &nbsp; 🙂

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-u0Gncfvbn1E/UEZt8gi1WbI/AAAAAAAAAJw/PeoLi80WZM4/s1600/photo.JPG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="240" src="http://1.bp.blogspot.com/-u0Gncfvbn1E/UEZt8gi1WbI/AAAAAAAAAJw/PeoLi80WZM4/s320/photo.JPG" width="320" /></a>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->