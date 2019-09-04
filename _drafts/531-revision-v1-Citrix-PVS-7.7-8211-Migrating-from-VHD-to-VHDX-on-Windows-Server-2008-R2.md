---
id: 1440
title: 'Citrix PVS 7.7 &#8211; Migrating from VHD to VHDX on Windows Server 2008 R2'
date: 2016-04-28T23:54:10-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/531-revision-v1/
permalink: /2016/04/28/531-revision-v1/
---
To migrate your VHD&#8217;s to VHDX&#8217;s you need:

<div>
  1) A server with Citrix Provisioning Server installed for the CVHDMount tool and sufficient space for a backup image
</div>

<div>
  2) Windows Server Backup
</div>

<div>
</div>

<div>
  Let&#8217;s Begin:
</div>

<div>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/1-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="157" src="http://theorypc.ca/wp-content/uploads/2016/01/1-1-300x148.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
  1) Mount your original VHD and your target VHDX
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a2-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="253" src="http://theorypc.ca/wp-content/uploads/2016/01/a2-1-300x237.png" width="320" /></a>
</div>

2) Start Windows Server Backup. &nbsp;Click &#8216;Backup Once&#8230;&#8217;

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a3-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="262" src="http://theorypc.ca/wp-content/uploads/2016/01/a3-1-300x246.png" width="320" /></a>
</div>

3) Select &#8216;Different options&#8217;

<div>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a4-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="265" src="http://theorypc.ca/wp-content/uploads/2016/01/a4-1-300x249.png" width="320" /></a>
</div>

4) Select &#8216;Custom&#8217;

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a5-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="266" src="http://theorypc.ca/wp-content/uploads/2016/01/a5-1-300x250.png" width="320" /></a>
</div>

&nbsp;5) Select your mounted VHD disk and click &#8216;OK&#8217;

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a6-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="263" src="http://theorypc.ca/wp-content/uploads/2016/01/a6-1-300x247.png" width="320" /></a>
</div>

&nbsp;6) Click &#8216;Next&#8217;

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a7-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="263" src="http://theorypc.ca/wp-content/uploads/2016/01/a7-1-300x247.png" width="320" /></a>
</div>

7) Choose the type of storage for the backup

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a8-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="263" src="http://theorypc.ca/wp-content/uploads/2016/01/a8-1-300x247.png" width="320" /></a>
</div>

8) Choose the backup destination

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a9-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="264" src="http://theorypc.ca/wp-content/uploads/2016/01/a9-1-300x248.png" width="320" /></a>
</div>

<div>
  9) Click &#8216;Backup&#8217;
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a10-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="264" src="http://theorypc.ca/wp-content/uploads/2016/01/a10-1-300x248.png" width="320" /></a>
</div>

<div>
  10) Wait for the backup to complete
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/BackupSpeed-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="147" src="http://theorypc.ca/wp-content/uploads/2016/01/BackupSpeed-1-300x111.png" width="400" /></a>
</div>

<div>
  The performance of the READ from the file share of the VHD was ~460-500Mbit/s
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a11-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="263" src="http://theorypc.ca/wp-content/uploads/2016/01/a11-1-300x247.png" width="320" /></a>
</div>

<div>
  11) Click &#8216;Close&#8217;
</div>

<div>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a12-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="252" src="http://theorypc.ca/wp-content/uploads/2016/01/a12-1-300x237.png" width="320" /></a>
</div>

<div>
  12) Click &#8216;Recover&#8230;&#8217;
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/a13-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2016/01/a13-1-300x234.png" width="320" /></a>
</div>

<div>
  13) Choose &#8216;This server&#8217; and click &#8216;Next&#8217;
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/b1-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2016/01/b1-1-300x234.png" width="320" /></a>
</div>

14) Click &#8216;Next&#8217;

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/b2-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="250" src="http://theorypc.ca/wp-content/uploads/2016/01/b2-1-300x235.png" width="320" /></a>
</div>

15) Select &#8216;Files and Folders&#8217; and click &#8216;Next&#8217;. &nbsp;I&#8217;ve selected &#8216;Files and Folders&#8217; instead of &#8216;Volumes&#8217; because I&#8217;ve found recovery to be easier. &nbsp;When doing a volume recovery I&#8217;ve found it fail if your volume is not \*slightly\* larger, which leads to a weird sort of arms race each time you have to do a reverse image.

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.46.24-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.46.24-2BPM-1-300x234.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

16) Select the drive click &#8216;Next&#8217;

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.47.21-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="248" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.47.21-2BPM-1-300x233.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

17) Select &#8216;Another Location&#8217; and browse your (formatted) VHDX drive and click &#8216;Next&#8217;.

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.48.22-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="250" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.48.22-2BPM-1-300x235.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
  18) Click &#8216;Recover&#8217;
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.49.43-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B9.49.43-2BPM-1-300x233.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
  19) Wait for the Recovery to finish&#8230;</p> 
  
  <p>
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.05.32-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.05.32-2BPM-1-300x234.png" width="320" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    20) Click &#8216;Close&#8217;
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.07.22-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="280" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.07.22-2BPM-1-300x263.png" width="320" /></a>
  </div>
  
  <p>
    21) Switch to the drive you just recovered; we need to modify the BCD store.
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.09.07-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.09.07-2BPM-1-292x300.png" width="310" /></a>
  </div>
  
  <p>
    22) Set the partition to your VHDX partition for, at least, these three settings ({bootmgr}-device, {default}-device, and {default}-osdevice).
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.13.12-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="80" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.13.12-2BPM-1-300x75.png" width="320" /></a>
  </div>
  
  <p>
    23) Set your partition as &#8216;Active&#8217;
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.11.14-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-20-2Bat-2B10.11.14-2BPM-1-247x300.png" width="263" /></a>
  </div>
  
  <p>
    24) Unmount your Citrix images.
  </p>
  
  <p>
    25) Done! &nbsp;Or so I thought. &nbsp;One of the things I&#8217;m not familiar with is whether Windows Server 2008 R2 is limited in some capacity for PVS 7.7 and VHDX files. &nbsp;2008 R2 does not natively support VHDX files so I am hoping it all works as Citrix *implies* VHDX files are supported in this scenario; or at least, there is nothing I can find explicitly saying it&#8217;s *not* supported.
  </p>
  
  <p>
    So, I created my first VHDX file with these settings:
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.36.13-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.36.13-2BAM-1-200x300.png" width="213" /></a>
  </div>
  
  <p>
    I thought 4K sector size would be good as that&#8217;s usually what&#8217;s recommended to align to these massive storage systems we have now. &nbsp;I mounted my disk using cvhdmount.exe and formatted it with Explorer.exe. &nbsp;I tried booting and I got nothing but garbage characters:
  </p>
  
  <table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
    <tr>
      <td style="text-align: center;">
        <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B12.50.25-2BAM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="220" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B12.50.25-2BAM-1-300x206.png" width="320" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        Literally, garbage
      </td>
    </tr>
  </table>
  
  <p>
    Well crap. &nbsp;So what&#8217;s going on here? &nbsp;I can mount my disk just fine. &nbsp;Maybe it&#8217;s the sector size? &nbsp;I created a new VHDX but opted for 512B. &nbsp;I then tried to mount it but it was *painfully slow*. &nbsp;It took several disk management rescans for it to find the disk and just outright refused to mount it. &nbsp;I suspected that because we host our files on a file share maybe that was the issue?
  </p>
  
  <p>
    I copied the 512B VHDX file to my C: drive and tried cvhdmount.exe again. &nbsp;It mounted quick and without issue. &nbsp; I formatted it and copied over bootmgr.exe it and this is what I got:
  </p>
  
  <table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
    <tr>
      <td style="text-align: center;">
        <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.47.33-2BAM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="221" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.47.33-2BAM-1-300x207.png" width="320" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        This is actually a good, successful message
      </td>
    </tr>
  </table>
  
  <p>
    Ok, good news, we CAN boot VHDX files but for some reason, only 512B sector VHDX files. &nbsp;I started having suspicions that this is related to a 4K sector size thing so I remounted the 4K sector VHDX file and checked the alignment with diskpart:
  </p>
  
  <table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
    <tr>
      <td style="text-align: center;">
        <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.50.25-2BAM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="213" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B1.50.25-2BAM-1-300x200.png" width="320" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
      </td>
    </tr>
  </table>
  
  <p>
    The disk reported back that it WAS 4K aligned. &nbsp;I&#8217;m running out of ideas.
  </p>
  
  <p>
    What I&#8217;ve&nbsp;<a href="https://support.microsoft.com/en-us/kb/2510009">found, though, is Windows 2008 R2 doesn&#8217;t actually support &#8216;native&#8217; 4K&nbsp;</a>sector sizes:
  </p>
  
  <div style="clear: both; text-align: center;">
    <a href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B3.20.01-2BAM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="217" src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-21-2Bat-2B3.20.01-2BAM-1-300x203.png" width="320" /></a>
  </div>
  
  <p>
    It does NOT support *booting* from native 4K sector disks either. &nbsp;This is probably my issue. &nbsp;Microsoft does state that the 4K sectors with 512e emulation *does* work with 2008 R2 but you need to <a href="https://www.microsoft.com/en-ca/download/details.aspx?id=24990">install a patch,</a> and the offset of logical vs physical sector size may impact performance. &nbsp;It appears by &#8216;</div> <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->