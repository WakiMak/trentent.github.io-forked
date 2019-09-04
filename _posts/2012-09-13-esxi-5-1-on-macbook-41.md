---
id: 673
title: ESXi 5.1 on MacBook 4,1
date: 2012-09-13T10:12:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/09/13/esxi-5-1-on-macbook-41/
permalink: /2012/09/13/esxi-5-1-on-macbook-41/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/09/esxi-51-on-macbook-41.html
blogger_internal:
  - /feeds/7977773/posts/default/1717190002627084564
categories:
  - Blog
  - Uncategorized
tags:
  - Uncategorized
---
Well... &nbsp;I got ESXi 5.0 installed on the MacBook but I was unable to get Mountain Lion to work. &nbsp;VMWare just came out with ESXi 5.1 which [supports Mountain Lion](https://blogs.vmware.com/guestosguide/2012/09/mac-os-x-10-8-mountain-lion.html) so I attempted to install ESXi 5.1 on a USB key and get it up and running on my MacBook. &nbsp;5.1 was a little different from 5.0 as the installerhelper.sh appears to operate differently...? &nbsp;Anyways, what I had to do was slipstream the sky2 driver into the ESXi 5.1 image, boot into "ESXi 'No Network Adapters Found'" then Fn-Option-F1 into the console. &nbsp;I then typed the following commands:

vmkload_mod sky2  
esxcfg-init -n  
vmkload_mod lvmdriver  
install

Without the lvmdriver the install would not see the storage (USB or HDD)

I am now installing ESXi 5.1 onto my MacBook. &nbsp;Hopefully, I'll be able to run Mountain Lion from it 🙂



<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->