---
id: 1430
title: ESXi 5.1 on MacBook 4,1
date: 2016-04-28T23:44:51-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/673-revision-v1/
permalink: /2016/04/28/673-revision-v1/
---
Well&#8230; &nbsp;I got ESXi 5.0 installed on the MacBook but I was unable to get Mountain Lion to work. &nbsp;VMWare just came out with ESXi 5.1 which [supports Mountain Lion](https://blogs.vmware.com/guestosguide/2012/09/mac-os-x-10-8-mountain-lion.html) so I attempted to install ESXi 5.1 on a USB key and get it up and running on my MacBook. &nbsp;5.1 was a little different from 5.0 as the installerhelper.sh appears to operate differently&#8230;? &nbsp;Anyways, what I had to do was slipstream the sky2 driver into the ESXi 5.1 image, boot into &#8220;ESXi &#8216;No Network Adapters Found'&#8221; then Fn-Option-F1 into the console. &nbsp;I then typed the following commands:

vmkload_mod sky2  
esxcfg-init -n  
vmkload_mod lvmdriver  
install

Without the lvmdriver the install would not see the storage (USB or HDD)

I am now installing ESXi 5.1 onto my MacBook. &nbsp;Hopefully, I&#8217;ll be able to run Mountain Lion from it 🙂



<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->