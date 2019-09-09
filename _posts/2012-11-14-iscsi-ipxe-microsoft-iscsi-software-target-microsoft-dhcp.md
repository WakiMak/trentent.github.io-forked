---
id: 669
title: iSCSI, iPXE, Microsoft iSCSI Software Target, Microsoft DHCP
date: 2012-11-14T11:45:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/11/14/iscsi-ipxe-microsoft-iscsi-software-target-microsoft-dhcp/
permalink: /2012/11/14/iscsi-ipxe-microsoft-iscsi-software-target-microsoft-dhcp/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/11/iscsi-ipxe-microsoft-iscsi-software.html
blogger_internal:
  - /feeds/7977773/posts/default/8731023135412148846
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - DHCP
  - iSCSI
  - PXE
---
I was recently struggling to get iSCSI working for myself.  Here were my problems and how I eventually fixed them.

To get iSCSI working the first thing I did was install the Microsoft Software Target 3.3 on a server machine. It's pretty simple.  Go to iSCSI Targets and create a new target.  Whatever you name it will be on the IQN so I've kept it short.  The iSCSI Initators Identifiers is like MAC Address filtering for a DHCP server, if you don't match you can't connect to the iSCSI target.  So set the SII to whatever IP, MAC Address or IQN you plan on using.  If you're like me and wasn't sure what the IQN will be set to, preferring to have iPXE's generated one; the Windows event viewer Application log will tell you what the IQN is when it fails to connect.  Look for Event ID 22.

<img src="http://2.bp.blogspot.com/-Ve-Gge76yvY/UKO5F0C6i0I/AAAAAAAAAKM/JNvFyTDcqtY/s400/1.png">

Highlighted is the IQN that tried to connect and failed.  Add it to the SII and you'll be good to go the next time.

When you are done creating the target you need to create the Virtual Disk (VHD) for the target.  Right-click on the target and go "Create Virtual Disk for iSCSI Target" and follow the prompts until you have your vDisk associated with the target.

We are now done on the server side.  To test the connection I launched iSCSI initiator on the same server and tried to connect to the iSCSI Software Target. It had a "Target Error" as the error message.  To get around this I enabled loopback.


HKLM\Software\Microsoft\iSCSI Target

Value Name: AllowLoopBack

Type: REG_DWORD

Value: 1 (Default is 0)

This will enable you to connect to your iSCSI target.

I could now connect to the iSCSI target that resided on the same server and validate the software target was working correctly.

Now to get the client side working.  My goal with this is to PXE boot some client machines with differencing VHD's off a golden Windows 7 image.  I encountered numerous issues attempting to do this and I'll share with you my problems and how I solved them.

The first thing I did was configure PXE booting off the Microsoft DHCP server.  I was going to make it launch PXELinux prior to iPXE and may modify my DHCP to do so in the future, but below is a screenshot of my working settings:

<img src="http://4.bp.blogspot.com/-I3LMxGztydg/UKO7nbAKhaI/AAAAAAAAAKc/cEgc52S9LtM/s640/2.png">

I don't think 043 Vendor Specific Info is required.  I put it in when I was trying to get PXELinux working.  This is a working DHCP config.


When I was trying to get PXELinux working with iPXE I ran into numerous problems.  Before I get too far ahead here, this was my PXELinux default config file:

[]# These default options can be changed in the geniso script

SAY iPXE ISO boot image

TIMEOUT 30

DEFAULT ipxe.lkrn

LABEL ipxe.lkrn

 KERNEL ipxe.krn

 INITRD ipxe.ipxe
