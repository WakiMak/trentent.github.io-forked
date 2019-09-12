---
id: 644
title: Citrix Provisioning Services (PVS) 6.1 - Automatic vDisk Update "Update device failed to shutdown within the timeout period."
date: 2013-05-22T12:44:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/22/citrix-provisioning-services-pvs-6-1-automatic-vdisk-update-update-device-failed-to-shutdown-within-the-timeout-period/
permalink: /2013/05/22/citrix-provisioning-services-pvs-6-1-automatic-vdisk-update-update-device-failed-to-shutdown-within-the-timeout-period/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/citrix-provisioning-services-pvs-61.html
blogger_internal:
  - /feeds/7977773/posts/default/6838678413580853315
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
---
I have setup our PVS environment to execute the vDisk Automatic Update feature utilizing a custom script (update.bat).  This script does a bunch of things, resync's the time with NTP (to avoid daylight savings issues), refreshs GPO's, executes Windows Update, cleans up temp files, runs the PVS optimizer, etc.

Unfortunately this can take longer than 30 minutes.  For some reason, when executing the ESD client as "None" (aka, so a script runs) the "Update timeout" doesn't seem to take effect, instead the 30 minute shutdown timeout is on the clock.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-AZXpFhXtC34/UZ0RbbNrnoI/AAAAAAAAAQY/pndo4Dvt5bM/s1600/3.png"><img src="http://1.bp.blogspot.com/-AZXpFhXtC34/UZ0RbbNrnoI/AAAAAAAAAQY/pndo4Dvt5bM/s320/3.png" width="320" height="221" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      ESD client is set to none
    </td>
  </tr>
</table>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-Ibo4wTLHN0k/UZ0Qztd-JoI/AAAAAAAAAQM/_sP2yRK6yPY/s1600/1.png"><img src="http://1.bp.blogspot.com/-Ibo4wTLHN0k/UZ0Qztd-JoI/AAAAAAAAAQM/_sP2yRK6yPY/s320/1.png" width="320" height="222" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      You cannot increase the shutdown timeout beyond 30 minutes
    </td>
  </tr>
</table>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-JNjFFA2SnHU/UZ0Qzihe28I/AAAAAAAAAQI/sbAeaRFhI34/s1600/2.png"><img title="" src="http://1.bp.blogspot.com/-JNjFFA2SnHU/UZ0Qzihe28I/AAAAAAAAAQI/sbAeaRFhI34/s640/2.png" alt="2013-05-22 10:10:59,690 [10] INFO  Mapi.MapiIPC - [xipProcessor] Starting an update on (System.Collections.Generic.Dictionary&96;2[System.String,System.Object]) 2013-05-22 11:02:07,763 [10] ERROR Mapi.MapiIPC - [xipProcessor] [WSCTXBLD303T] Update device failed to shutdown within the timeout period. 2013-05-22 11:02:07,763 [10] INFO  Mapi.MapiIPC - [xipProcessor] [WSCTXBLD303T] Update device failed to shutdown within the timeout period. 2013-05-22 11:02:56,186 [10] ERROR Mapi.MapiIPC - [xipProcessor] [WSCTXBLD303T] Submit image failed (VM: WSCTXBLD303T, Image: XenApp65tn02, Update device failed to shutdown within the timeout period.) 2013-05-22 11:02:56,186 [10] INFO  Mapi.MapiIPC - [xipProcessor] [WSCTXBLD303T] Submit image failed (VM: WSCTXBLD303T, Image: XenApp65tn02, Update device failed to shutdown within the timeout period.)" width="640" height="40" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Total time for the updates was 49 minutes (10:11AM to 11:02AM)
    </td>
  </tr>
</table>

The only solution I have been able to come up with so far is set to run the updates in less than 30 minutes.  I think I'll attempt changing the "Update.bat" to "Preupdate.bat" and see if that avoids the "Shutdown timeout".

Unfortunately, I do not know when the shutdown timeout period starts or why it starts.  I was hoping the "Update timeout" was started when running the "Update.bat" file.  This does not appear to be so, sadly.

[Citrix documentation implies that you should work hard to keeping the timeout below 30 minutes.](http://support.citrix.com/proddocs/topic/provisioning-60/pvs-vdisks-update-vm-create-configure-esd.html)

"Citrix recommends to only apply updates that can be downloaded and installed in 30 minutes or less."

======================EDIT==================  
Preupdate.bat does not appear to operate under the Update Timeout either.

======================EDIT 2=================  
To increase the limit you need to create a registry key called "DiskProvider" and create a dword with the decimal value of the length of time you want the \*total\* time called "deviceShutdownTimeout".

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-RPpSAXRGvbY/UaUsCx9GbcI/AAAAAAAAASA/sy58Vvl4NH4/s1600/update1.PNG"><img src="http://4.bp.blogspot.com/-RPpSAXRGvbY/UaUsCx9GbcI/AAAAAAAAASA/sy58Vvl4NH4/s320/update1.PNG" width="320" height="130" border="0" /></a>
</div>

NOTE: This does NOT change the value in the GUI and will override the  value in the GUI, regardless of what it is set to.  You need to restart the SOAP service after making this change.  This registry key must exist on the PVS server that the site designates as the "vDisk Update Server"

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-KSzUBYN2bV4/UaUshJY_anI/AAAAAAAAASI/EG1FvD3ZLIA/s1600/update2.png"><img src="http://3.bp.blogspot.com/-KSzUBYN2bV4/UaUshJY_anI/AAAAAAAAASI/EG1FvD3ZLIA/s320/update2.png" width="320" height="208" border="0" /></a>
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->