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