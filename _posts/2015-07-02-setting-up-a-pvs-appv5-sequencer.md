---
id: 562
title: Setting up a PVS AppV5 sequencer
date: 2015-07-02T23:43:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/02/setting-up-a-pvs-appv5-sequencer/
permalink: /2015/07/02/setting-up-a-pvs-appv5-sequencer/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/setting-up-pvs-appv5-sequencer.html
blogger_internal:
  - /feeds/7977773/posts/default/3413367957568803980
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Citrix
  - Provisioning Services
---
A typical workflow for sequencing an application is something like this:  
1) Take a snapshot of your sequencer (odds are it&#8217;s a virtual machine [VM])  
2) Power up your sequencer and sequence your application  
3) Move your application to your system, test it, try and catalog all problems. &nbsp;Revert your VM snapshot and go to step 1.

In several environments that I have worked in, this is a bit of a hassle as most environments implement the &#8216;Machine Account Password Expires after X days&#8217;. &nbsp;So after that time you either need to delete your snapshot, power on your VM and rejoin domain then start over. &nbsp;This gets really cumbersome with multiple sequencers to take care of. &nbsp;In addition, at my current environment, we require Windows Update be up-to-date and applied to all machines, including sequencers. &nbsp;Reverting a snapshot to before Windows Update will, sometimes, cause Windows Update to start upon the desktop when you login. &nbsp;There is a lot of overhead and hassle to maintaining snapshots, though it is better than the alternative of reinstalling the operating system each time you want a clean sequencer.

There is a very elegant solution to all of this I have found that also allows me to sequence super fast.

### Citrix Provisioning Service (PVS). 

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-5cwM_wQ8t4s/VZYSSA1eTCI/AAAAAAAAA0w/FS3_LeR8MK0/s1600/4.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-5cwM_wQ8t4s/VZYSSA1eTCI/AAAAAAAAA0w/FS3_LeR8MK0/s1600/4.PNG" /></a>
</div>

Citrix PVS works by managing the Active Directory machine account password for me, so I never have to reset it again.

<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-RDfc65mrtec/VZYRvAgcIvI/AAAAAAAAA0o/XUXNMZU6QfE/s1600/3.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="311" src="http://2.bp.blogspot.com/-RDfc65mrtec/VZYRvAgcIvI/AAAAAAAAA0o/XUXNMZU6QfE/s320/3.PNG" width="320" /></a>
</div>

With Citrix PVS you can create several sequencers from one vDisk. &nbsp;This ensures consistency and avoids tech&#8217;s having their own VM with their own nuances.

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-Ag03TJmiBk0/VZYQ27rXczI/AAAAAAAAA0g/oAYNbzq9JiI/s1600/2.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="52" src="http://1.bp.blogspot.com/-Ag03TJmiBk0/VZYQ27rXczI/AAAAAAAAA0g/oAYNbzq9JiI/s320/2.PNG" width="320" /></a>
</div>

Citrix PVS allows you to create a version of the vDisk and apply Windows Update&#8217;s. &nbsp;Automatically, all of your sequencers get the updates applied.

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-gsU9P6LSru0/VZYQnP9tsZI/AAAAAAAAA0Y/GG7t0557eMc/s1600/1.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="218" src="http://4.bp.blogspot.com/-gsU9P6LSru0/VZYQnP9tsZI/AAAAAAAAA0Y/GG7t0557eMc/s320/1.PNG" width="320" /></a>
</div>

With &#8216;Standard Image&#8217; mode, the VM now becomes &#8216;non-persistent&#8217;. &nbsp;This means if you reboot your VM it will go back to the state it was when it was last powered on. &nbsp;PVS requires a &#8216;persistent&#8217; supplemental disk attached to the VM for things like the event log, page file and the write cache files. &nbsp;We ensure the supplemental disk is large enough so we can locally store application installer files there (temporarily!) then the installer files go onto a fileshare that has regular backups, etc.

Starting with Citrix PVS 7.1 there is a new feature called &#8216;Cache to device RAM with overflow on hard disk&#8217;. &nbsp;By utilizing a memory sizing for your sequencer VM that can run the operating system \*and\* contain the largest application in total megabyte size, your sequencing can go super duper fast! &nbsp;Sequencing to the operating system drive with this PVS feature will actually write to RAM, which is significantly faster than disk. &nbsp;This shortens sequencing times significantly. &nbsp;You will need to ensure your cache size is large enough for your largest application or else the overflow to disk will slow you down.

### But won&#8217;t this setup fail to work with AppV5&#8217;s &#8216;reboot&#8217; feature?

Starting with AppV5 you can now \*reboot\* an application in the middle of sequencing and the sequencer is supposed to be pickup after the reboot and continue to capture. &nbsp;So far, I have not had good luck continuing sequencing after rebooting with the AppV5 sequencer. &nbsp;I&#8217;ve found, with a 100% success rate \*so far\* that a reboot is not necessary. &nbsp;I&#8217;m aware that applications [like Chrome](http://blogs.technet.com/b/gladiatormsft/archive/2014/09/30/app-v-5-on-why-the-app-v-5-sequencer-really-reboots.aspx)&nbsp;show that a reboot can make a package work successfully, but I prefer to figure out what it does post-reboot and [just implement that in the sequence instead](http://stealthpuppy.com/the-case-of-the-disappearing-application-during-sequencing/).

By avoiding doing reboots we are not missing that feature at all. &nbsp;In cases where it is absolutely required  
I have a workaround. &nbsp;I create another vDisk version in Maintenance mode

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-pATDVV0Jce8/VZYX6giXqyI/AAAAAAAAA1A/hcByMMLkNWI/s1600/5.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="196" src="http://3.bp.blogspot.com/-pATDVV0Jce8/VZYX6giXqyI/AAAAAAAAA1A/hcByMMLkNWI/s320/5.PNG" width="320" /></a>
</div>

And set my Sequencer VM to&nbsp;Maintenance&nbsp;mode. 

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-ikpcojVtwCY/VZYYFB1uh9I/AAAAAAAAA1I/1Kif1NzmJPY/s1600/6.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="278" src="http://3.bp.blogspot.com/-ikpcojVtwCY/VZYYFB1uh9I/AAAAAAAAA1I/1Kif1NzmJPY/s320/6.PNG" width="320" /></a>
</div>

This turns the VM to a &#8216;persistent&#8217; state for this duration. &nbsp;Once the sequence is completed I delete the version and set my VM back to production. &nbsp;The caveat here is only one VM may use the maintenance version at a time.

### What should I put on my PVS Sequencer?

As per my previous topic, I believe you should try and minimize the visual C++ runtimes that are different from your sequencer to your VDI/XenApp server boxes. &nbsp;If possible, make them identical. &nbsp;This prevents AppV5 from usurping installed versions of this runtimes when pushed to your VDI/XenApp servers. &nbsp;They may still get usurped if an application has a different version installed then what you have on your box, but I strongly believe in minimizing it if possible. &nbsp;There may also be a (small) benefit by speeding up your application first launch experience by removing some minor registry staging.

Here is what I have on my sequencer and a clipping of some of the software we run on our XenApp servers:

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-Ca4w62i4uf4/VZYbNStIkNI/AAAAAAAAA1Y/KCRNqlXmM0E/s1600/9.PNG" style="margin-left: auto; margin-right: auto;"><img border="0" height="194" src="http://3.bp.blogspot.com/-Ca4w62i4uf4/VZYbNStIkNI/AAAAAAAAA1Y/KCRNqlXmM0E/s320/9.PNG" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      All of the software on the Sequencer
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-bGCvMa6lQAs/VZYbXmb7-sI/AAAAAAAAA1k/Scn4JFU-Bmw/s1600/7.PNG" style="margin-left: auto; margin-right: auto;"><img border="0" height="187" src="http://2.bp.blogspot.com/-bGCvMa6lQAs/VZYbXmb7-sI/AAAAAAAAA1k/Scn4JFU-Bmw/s320/7.PNG" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      A small clipping of software on the XenApp servers
    </td>
  </tr>
</table>

<div style="clear: both; text-align: center;">
</div>

With the PVS sequencer you have a static, persistent disk that you can use to locally store installer files, notes, AppV template file, whatever you need to help you get sequencing.

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-Z7jvPSIGa1o/VZYcB__GsFI/AAAAAAAAA1s/xftR7Yu8INY/s1600/10.PNG" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="182" src="http://4.bp.blogspot.com/-Z7jvPSIGa1o/VZYcB__GsFI/AAAAAAAAA1s/xftR7Yu8INY/s320/10.PNG" width="320" /></a>
</div>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://4.bp.blogspot.com/-zvSXqfpek98/VZYcpVjPrRI/AAAAAAAAA14/9qChpAVigpo/s1600/11.PNG" style="margin-left: auto; margin-right: auto;"><img border="0" height="320" src="http://4.bp.blogspot.com/-zvSXqfpek98/VZYcpVjPrRI/AAAAAAAAA14/9qChpAVigpo/s320/11.PNG" width="246" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      A little snapshot of my persistent disk
    </td>
  </tr>
</table>



### How do I sequence with this thing?

<div>
  For my sequences, I usually create a batch file that lets me do a silent install of the application and any shortcut configuration. &nbsp;I can then use this batch file to do a completely scripted sequence if required in the future. &nbsp;Otherwise, sequencing is identical to how you would do it previously, but you cannot restart the system! &nbsp;Restarting will result in a clean sequencer coming back up (after all, that&#8217;s the point!)
</div>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->