---
id: 2967
title: Citrix and large desktops
date: 2019-03-05T09:35:31-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2967
permalink: /?p=2967
categories:
  - Uncategorized
---
With the rise of 4K monitors and video cards that can handle multiple large displays we now require more resources than ever to be productive.Â Pushing these boundaries leads to finding that default settings are now restricting you in weird and interesting ways.

I have an uncommon setup.

Two 4k monitors and one 2.5k monitor

<img class="aligncenter size-full wp-image-2968" src="http://theorypc.ca/wp-content/uploads/2019/03/Setup.png" alt="" width="1200" height="1056" srcset="http://theorypc.ca/wp-content/uploads/2019/03/Setup.png 1200w, http://theorypc.ca/wp-content/uploads/2019/03/Setup-300x264.png 300w, http://theorypc.ca/wp-content/uploads/2019/03/Setup-768x676.png 768w" sizes="(max-width: 1200px) 100vw, 1200px" /> 

&nbsp;

This gives me a total desktop area of 7680 pixels wide and 3600 pixels high.Â Even though the total area of all monitors combined equals 20275200 pixels the total size is actually 27648000 pixels &#8212; 36% more &#8212; with the regions to the left and right of monitor 3 simply blacked out and marked unusable by the interface.

This staggering number of pixels in my setup easily exceeds Citrix&#8217;s default memory limit.Â Citrix has a [great article on how to calculate this limit](https://support.citrix.com/article/CTX115637).

In order to support my configuration the limit needs to be adjusted.Â In order to support my setup I need the following memory limit:

7680 x 3600 x (32bits / 8) ==Â 110592000 total bytes

110592000 / 1024 =Â 108000KB (or 105.4MB)

Citrix supports a limit of 4GB of total display memory (setting to this value removes the limit).Â However, simply doubling the value from 65MB to 128MB will cover a 4 monitor 4K setup, providing you setup the monitors in a sane way,Â If you were to put the monitors in a diagonal fashion than you would require even more memory as each would monitor would square the total area.

So what happens when you have a larger area then the memory limit?

Weird things.Â Allow me to demonstrate:

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->