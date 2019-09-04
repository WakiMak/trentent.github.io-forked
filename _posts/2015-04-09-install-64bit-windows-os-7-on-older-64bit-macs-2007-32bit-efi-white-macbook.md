---
id: 565
title: 'Install 64bit Windows OS (7+) on older 64bit Mac's (2007 32bit EFI white MacBook)'
date: 2015-04-09T16:55:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/04/09/install-64bit-windows-os-7-on-older-64bit-macs-2007-32bit-efi-white-macbook/
permalink: /2015/04/09/install-64bit-windows-os-7-on-older-64bit-macs-2007-32bit-efi-white-macbook/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/04/install-64bit-windows-os-7-on-older.html
blogger_internal:
  - /feeds/7977773/posts/default/582466947184097732
categories:
  - Blog
  - Uncategorized
tags:
  - Uncategorized
---
When working at HP I came up with a Windows Vista restore/install solution that worked using Windows PE for both 32bit and 64bit architectures. Â A few years ago, I tried the same solution against my 2007 White MacBook, and, lo and behold, I was able to install 64bit Windows 7 on my MacBook. Â I didn't have the time to document it as it was just for curiosity, but I am doing so here now because I am repurposing my MacBook as a 3D Printer server.

To start, you need a 32bit Windows install on DVD and the version of Windows you want to install on a USB key (or another DVD). Â This is because the MacBook will not DVD boot off of a 64bit install, or the USB key.

1) Boot off the 32bit install DVD.

2) Shift-F10 into a command prompt.

3) Format your hard drive/partition. Â Make sure you only have 1 partition. Â You may need to use diskpart, whenever I used the Windows Setup it created 2 partitions which causes 0xc000000f errors on startup because the BCD file is configured to look for [boot]pathtowim which doesn't reside on the boot drive.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-DN0CuLfAyPk/VScDRzkbg8I/AAAAAAAAAxQ/E-hTLNluoek/s1600/FullSizeRender.jpg"><img src="http://1.bp.blogspot.com/-DN0CuLfAyPk/VScDRzkbg8I/AAAAAAAAAxQ/E-hTLNluoek/s1600/FullSizeRender.jpg" width="320" height="240" border="0" /></a>
</div>

4) Â Robocopy everything from the DVD to the HDD. (My DVD drive came up as E:)  
Robocopy E:\ C:\ /MIR

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-kF1Nd2_dpDI/VScDV8qzmqI/AAAAAAAAAxY/tXTuNCQ0QBA/s1600/FullSizeRender%2B(1).jpg"><img src="http://1.bp.blogspot.com/-kF1Nd2_dpDI/VScDV8qzmqI/AAAAAAAAAxY/tXTuNCQ0QBA/s1600/FullSizeRender%2B(1).jpg" width="320" height="240" border="0" /></a>
</div>

5) Insert your Windows x64 USB/DVD and copy over \*just\* the install.wim from %USB%\sources\install.wim to your C:\sources\install.wim

6) Eject the DVD and reboot your computer off the hard drive. Â Do the install as usual.

This trick only seems to work when you have both 32bit versions and 64bit versions of the SAME software. Â It does not appear to work if you try, for instance, to use the Windows 7 Enterprise x86 and Windows 7 Ultimate x64.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-_GagkfxPJ5I/VScFbPoutVI/AAAAAAAAAxk/MHPvTwCJIB8/s1600/macbook-x64.PNG"><img src="http://2.bp.blogspot.com/-_GagkfxPJ5I/VScFbPoutVI/AAAAAAAAAxk/MHPvTwCJIB8/s1600/macbook-x64.PNG" width="320" height="195" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-AsDFCYp99WY/VScFcODrZMI/AAAAAAAAAxs/gsrRardLsrA/s1600/macbook-x64mobo.PNG"><img src="http://2.bp.blogspot.com/-AsDFCYp99WY/VScFcODrZMI/AAAAAAAAAxs/gsrRardLsrA/s1600/macbook-x64mobo.PNG" width="320" height="194" border="0" /></a>
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->