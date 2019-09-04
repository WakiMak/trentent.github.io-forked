---
id: 1575
title: 'Citrix Provisioning Server performance &#8211; target device bootup process -> client optimization'
date: 2016-07-04T22:31:21-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1575
permalink: /?p=1575
categories:
  - Blog
---
In my previous two posts we quantified how the boot process works and then we looked at modifying MTU on the PVS server to determine if it had any appreciable benefit. Â For Gen1 Hyper-V VM&#8217;s it does not. Â This post will explore the client side performance with the use of the Windows Performance Toolkit.

Step 1 is to install the [Windows Performance Toolkit](http://go.microsoft.com/fwlink/p/?LinkId=526740). Â For some reason, Microsoft decided it&#8217;s best if you download the ADK to install the performance toolkit. You&#8217;ll need to do this in &#8216;Private&#8217; or &#8216;Maintenance&#8217; mode because the boot config needs to persist across reboots.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->