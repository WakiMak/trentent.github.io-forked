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

&#35; These default options can be changed in the geniso script  
SAY iPXE ISO boot image  
TIMEOUT 30  
DEFAULT ipxe.lkrn  
LABEL ipxe.lkrn  
 KERNEL ipxe.krn  
 INITRD ipxe.ipxe  

Can you spot the error already?


&#35;!ipxe  
  
  dhcp  
  set keep-san 1  
  sanhook --drive 0x80 iscsi:192.168.1.2::::iqn.1991-05.com.microsoft:aclgdc01-t1-target  
  sanboot --nodescribe --drive 0xe0 http://192.168.1.2/win7/winpe.iso  


My ipxe.ipxe script file.  Everything I read said this should work.


PXELinux was booting the iPXE kernel, The iSCSI target wasn't detected natively by the Windows 7/2008 DVD's (which everything I read said that it should be).  To correct this I made a modified WinPE with the iSCSI Initiator builtin.  When I booted up that WinPE I could start the MSISCSI service and connect to the target.  I thought things looked good and I could format the target and make folders/files on it.  I could even bootsect it.  I then mapped a network drive to the Windows installer files (which iPXE recommends) then ran setup.exe.  I got to the part where I could choose the hard disk but Windows wouldn't let me install on it giving me an error message.

"Windows cannot be installed to Disk <#> Partition <#>. (Show details)" -> "Windows cannot be installed to this disk. iSCSI deployment is disabled since no NICs referenced in the iBFT can be resolved to actual NT-visible devices. Windows cannot be installed to this disk. This computer's hardware may not support booting to this disk.

Alright, at least I was making progress.  From here I had to find out what a iBFT was and why can't Windows resolve it when I'm staring straight at the hard disk.  The importance of it is found here.

"When the pre-boot loader starts, it loads a real-mode network stack driver. The loader contains an iSCSI initiator which logs on to the iSCSI target and mount the boot disk to the system. Another function of the loader is to populate the iSCSI Boot Firmware Table (iBFT), which is required for iSCSI boot. The Boot Parameter driver in Windows will load the parameters from the iBFT, and the Microsoft iSCSI Software Initiator will be able to connect to the iSCSI target using the parameters set in the iBFT. The importance of the iBFT is to be able to share the parameters between the iSCSI boot initiator (which establishes the session in the preboot phase) and the Microsoft iSCSI initiator (which establish the session after Windows boots)."

So my iPXE isn't storing it's values in the iBFT.  This seemed weird to me because I could see it preserving the connection when I booted.  Everything I read said the "set keep-san 1" is the magic to storing the iPXE values in the iBFT.

<img src="http://1.bp.blogspot.com/-IepEu75i37I/UKPTu0b8eaI/AAAAAAAAAKs/ipmnBx-4VZE/s400/3.png">
Isn't that what Registered SAN Device 0x80 means?  That it's registered in the iBFT? 

So it appeared to be working and I thought maybe it's Hyper-V.  I read other people using Hyper-V were able to get this to work, but I'm stumped.  It won't work for me.  So I tried VMWare Player.  I had the same issue there.  No NICs referenced in the iBFT even though I could use the MS Initiator to connect to the target.

At this point I was getting pretty frustrated.  For all intent and purposes this should work.  So I went back to ipxe.org and read their Windows Deployment Service guide and setup WDS and made a WDS wim thinking maybe it had some magic touch; even though I had read that the standard install media should work.

Well, the WDS image didn't work either.  At this point I went back to ipxe.org and followed their PXE Chainloading guide exactly.  Well, mostly exactly.  I didn't want to make a reservation so I applied the setting across the entire DHCP by entering everything in Server Options (see the first screenshot of this post).

Like magic, as soon as I changed the kernel from iPXE.KRN to undionly.kpxe it showed me something different on the Hyper-V console:

<img src="http://3.bp.blogspot.com/-DSjsdpLYTwU/UKPWLOovtnI/AAAAAAAAAK0/C3U8OGhf70E/s400/4.png">
This *feels* more promising.


Like Magic the undionly.kpxe kernel spewed forth more information than the iPXE.KRN.  I now had a problem where I wanted to boot from the local CDROM and not from the SAN Device, but *after* it became registered.  This is my http://192.168.1.2/win7/ipxe.ipxe script:


&#35;!ipxe


&#35;  set skip-san-boot 1  
set keep-san 1  

If I uncomment skip-san-boot then it will boot from the local CD-ROM.  You'll need to recomment it if you want it to boot from the SAN.  Magically, the Windows 7 install DVD picked up the SAN device during the install, no hacked WinPE required.

And that's it.  Booting from the UNDIONLY.KPXE works where iPXE.KRN does not.

And that will get you a iSCSI booting disk.  I suspect I can change PXELinux to boot the UNDIONLY.KPXE.  I prefer to use PXELinux because if I want to burn a bootable CD/DVD or USB stick I can just transfer my entire PXELinux folder structure over for SYSLinux or ISOLinux as a boot loader.

Next I'm going to try differencing disks.  The goal for this little project is to see if I can setup a render farm by iSCSI booting computers and have them join automatically.  This way if you need more power added to your farm you can just add any hardware, boot it up via PXE and it will launch into an environment where everything is pre-configured and ready to go.


#UPDATE  
I've been able to get pxelinux to boot iPXE.KRN.  The trick is the ipxe launch file needs the net0 to have the keep-san set.  Setting it globally didn't appear to make it set in net0 (which, I assume, becomes stored in the iBFT)

This is my ipxe.ipxe that works with iPXE.KRN after been chainloaded by pxelinux.0:


&#35;!ipxe  
dhcp  
sanhook ${root-path}  
set net0/skip-san-boot 1  
set net0/keep-san 1  
prompt --key 0x02 --timeout 4000 Press Ctrl-B for the iPXE command line... && shell ||  
exit  

I set the skip-san-boot so I could boot from a regular Windows CD.  You'll want to remove that line to actually boot from the SAN.

Keep in mind there is a gateway issue with MS's iSCSI implementation.  It will add a static route to the SAN  IP so you will either need to clear the gateway via the iPXE script or have the software target on the same location as your router (or have your router route local LAN traffic).
