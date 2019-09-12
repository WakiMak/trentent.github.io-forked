---
id: 718
title: Remotely wake computers when the network doesn't pass Magic Packets
date: 2010-09-15T00:17:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2010/09/15/remotely-wake-computers-when-the-network-doesnt-pass-magic-packets/
permalink: /2010/09/15/remotely-wake-computers-when-the-network-doesnt-pass-magic-packets/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2010/09/remotely-wake-computers-when-network.html
blogger_internal:
  - /feeds/7977773/posts/default/6227876442161744357
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
I'm in a situation where the network we have doesn't pass the UDP 9 magic packet to WoL (wake-on-LAN) remote computers. I'm not sure why this is, but I've developed a solution around it. It requires you to be a domain administrator (or local admin) because it uses PSEXEC and you need to copy a small command-line program to the local machine to execute. To do this in such a way that you can do multiple computers in one pass, you need to create a text file in the same folder as the batch file (upcoming). The text file has the following format:

<div>
</div>

> <div>
>   <span style="font-family: 'courier new';">"COMPUTER-NAME-OF-SYSTEM-ON-SAME-LAN MAC-ADDRESS"</span>
> </div>

<div>
  Note the space between the two. An example:
</div>

<div>
  CAC00700ZZ is off, but it has a MAC address of 00:12:34:56:78
</div>

<div>
  CAC00855FT is on and on the same LAN as CAC00700ZZ. I'm on computer SERVER1 and the link between SERVER1 and CAC00700ZZ doesn't allow the magic packet to traverse. To wake up CAC00700ZZ, I need to send a WoL packet from CAC00855FT. To do this I create a text file called "computer-list.txt" and put in:
</div>

<div>
</div>

<div>
  <blockquote>
    <p>
      CAC00855FT 00:12:34:56:78
    </p>
  </blockquote>
</div>

<div>
</div>

<div>
  Done.
</div>

<div>
</div>

<div>
  Next I need to create a WAKEUP.CMD file and put in the following:
</div>

<div>
  <div>
  </div>
  
  <blockquote>
```shell
:This script wakes up computers using a tertiary computer on the same LAN.

for /F "tokens=1-2 delims= " %%A IN ('type computer-list.txt') do psexec \\%%A -i -c mc-wol.exe %%B
pause
```
  </blockquote>
  
  <div>
  </div>
</div>

<div>
</div>

<div>
  Lastly, I need to ensure PSEXEC.EXE, MC-WOL.EXE, computer-list.txt and WAKEUP.CMD are in the same folder. <a href="http://www.matcode.com/wol.htm">MC-WOL.EXE can be downloaded from here</a>. PSEXEC.exe can be downloaded from Microsoft.
</div>

<div>
</div>

<div>
  If you needed to wake up multiple computers, your computer-list.txt file would look like this:
</div>

<div>
  <div>
  </div>
  
  <blockquote>
    <div>
      <span style="font-family: 'courier new';">CAC00700JQ 18A9051BFE68</span>
    </div>
    
    <div>
      <span style="font-family: 'courier new';">CAC00700HT 18A9051E2894</span>
    </div>
    
    <div>
      <span style="font-family: 'courier new';">CAC00700HQ 18A9051E2B90</span>
    </div>
  </blockquote>
  
  <div>
  </div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->