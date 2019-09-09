---
id: 642
title: (OS 10061)No connection could be made because the target machine actively refused it.  Unable to connect to the CGP tunnel destination (127.0.0.1 1494)
date: 2013-05-27T15:55:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/27/os-10061no-connection-could-be-made-because-the-target-machine-actively-refused-it-unable-to-connect-to-the-cgp-tunnel-destination-127-0-0-11494/
permalink: /2013/05/27/os-10061no-connection-could-be-made-because-the-target-machine-actively-refused-it-unable-to-connect-to-the-cgp-tunnel-destination-127-0-0-11494/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/os-10061no-connection-could-be-made.html
blogger_internal:
  - /feeds/7977773/posts/default/7556182004236996476
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Group Policy
  - Performance
  - Provisioning Services
  - scripting
---
&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-uhibNRUz-Bw/UaPTxUnsQUI/AAAAAAAAARA/RyeXioTfan8/s1600/3.PNG"><img src="http://3.bp.blogspot.com/-uhibNRUz-Bw/UaPTxUnsQUI/AAAAAAAAARA/RyeXioTfan8/s320/3.PNG" width="320" height="214" border="0" /></a>
</div>


This has been an ongoing problem for us (Unable to connect to the CGP tunnel destination (127.0.0.1:1494)

I may have found out why it was happening in our environment.  We are using Provisioning Services and with it we are using two NIC's, one for the Provisioning Services and one for Standard networking.

It appears the XTE service became configured to use the Provisioning Services NIC.  This was verified in the httpd.conf in the C:\Program Files (x86)\Citrix\XTE\conf folder.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-mMHMOaXNXjk/UaPTxfp3-CI/AAAAAAAAAQ8/S0PKFWf0Qik/s1600/4.PNG"><img src="http://2.bp.blogspot.com/-mMHMOaXNXjk/UaPTxfp3-CI/AAAAAAAAAQ8/S0PKFWf0Qik/s320/4.PNG" width="320" height="156" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Provisioning NIC and Production (network) NIC
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-tgKUfaBI32A/UaPTxQeWTWI/AAAAAAAAAQ4/qpfEBbd0pMo/s1600/5.PNG"><img src="http://4.bp.blogspot.com/-tgKUfaBI32A/UaPTxQeWTWI/AAAAAAAAAQ4/qpfEBbd0pMo/s320/5.PNG" width="320" height="218" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      httpd.conf as was when the system booted (and non-functional)
    </td>
  </tr>
</table>

When I traced the XTE service using procmon.exe and wireshark with this non-functional conf this is what I saw when I attempted to launch the application:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-9P7PbI5Gm_c/UaPTxybccsI/AAAAAAAAARI/XXIhJ0Tk1eQ/s1600/broken-connection.PNG"><img src="http://3.bp.blogspot.com/-9P7PbI5Gm_c/UaPTxybccsI/AAAAAAAAARI/XXIhJ0Tk1eQ/s320/broken-connection.PNG" width="320" height="68" border="0" /></a>
      </td>
    </tr>
    <tr>
      <td style="text-align: center;">
        You can see it attempt to connect to itself via 1494 but then nothing else happens
      </td>
    </tr>
  </table>

  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td style="text-align: center;">
        <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-IqAz9FYtugI/UaPTxwE66KI/AAAAAAAAARM/T5JB4aiZu9g/s1600/bad-wireshark.PNG"><img src="http://1.bp.blogspot.com/-IqAz9FYtugI/UaPTxwE66KI/AAAAAAAAARM/T5JB4aiZu9g/s320/bad-wireshark.PNG" width="320" height="74" border="0" /></a>
      </td>
    </tr>
    <tr>
      <td style="text-align: center;">
        Wireshark shows virtually nothing on the network and nothing related to IMA
      </td>
    </tr>
</table>
  

  
 When I edited the file to have the Production NIC...
 
![](http://2.bp.blogspot.com/-zFFprXLYhLU/UaPTxyU25qI/AAAAAAAAARE/a8aQNFDX1gs/s320/6.PNG "")

 
then restarted the XTE service and retraced via Procmon and Wireshark...


    
<div style="clear: both; text-align: center;">
      <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Xa-mdGivUHk/UaPTyC3WpRI/AAAAAAAAARg/yUBa2ZzNq7g/s1600/working-connection.PNG"><img src="http://4.bp.blogspot.com/-Xa-mdGivUHk/UaPTyC3WpRI/AAAAAAAAARg/yUBa2ZzNq7g/s320/working-connection.PNG" width="320" height="185" border="0" /></a>
</div>
    
<div style="clear: both; text-align: center;">
      <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-_54KbcVmV4I/UaPTybMVHeI/AAAAAAAAARk/ElcLzqY-99s/s1600/working-wireshark.PNG"><img src="http://4.bp.blogspot.com/-_54KbcVmV4I/UaPTybMVHeI/AAAAAAAAARk/ElcLzqY-99s/s320/working-wireshark.PNG" width="320" height="92" border="0" /></a>
</div>
    
   
   
 We now see tons of activity and the application now launches without issues.</p> 
      

================EDIT===============

We have now found why we are getting this error, and why we are getting it intermittently.  The issue is we are using PVS with multi-homed NIC's.  One NIC (LanAdapter 1) is the "Provisioning" network, and the second NIC (LanAdapter 2) is the "Production" network.  The Provisioning network is on a completely seperate vLan and sees no traffic outside of it's little network.  The ICA Listener was attaching itself to the Provisioning network instead of the production network, so when we tried to connect to the server it would fail with the CGP tunnel error because the outside network cannot talk to the Provisioning network.  To attempt to resolve this issue one of our techs (Saman) created a group policy preference registry key that set the following value (HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Terminal Server\WinStations\ICA-TCP - LanAdapter):
 
      
<div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-fzfxPbP9xL4/UaUt4CSMiXI/AAAAAAAAASY/Mx77cMOe_pA/s1600/update3.PNG"><img src="http://1.bp.blogspot.com/-fzfxPbP9xL4/UaUt4CSMiXI/AAAAAAAAASY/Mx77cMOe_pA/s320/update3.PNG" width="320" height="159" border="0" /></a>
      </div>
      
By setting it to "2" we could ensure the ICA listener is always listening on LanAdapter 2, our production network.  Unfortunately, a Windows Update appears to have caused either Group Policy Registry Preferences to execute (sometimes) *after* the IMAService service started, or allowed the IMAService service to start *before* Group Policy Registry Preferences.  IMAService will recreate that file every second restart.  To resolve this issue I created a startup script that executes after 65 seconds, deleting the httpd.conf file and restarting the appropriate services until the httpd.conf file is recreated.

In my testing it appears you need to restart the "IMAService" service twice to get it to recreate the httpd.conf file.  Because of this, I created the script to retry up to 3 times to try and regenerate the file.

      
<pre class="lang:batch decode:true ">:: ===========================================================================================================
::
:: Created by:  Trentent Tye
::   
::   
::
:: Creation Date: May 28, 2013
:: Modified Date: May 28, 2013
::
:: File Name:  Citrix_Restart_IMASrv_Delayed.cmd
::
:: Description:  This script fixes the CGP Tunnel issue where the IMASrv.exe starts before
::   group policy prefencess start.  This causes the httpd.conf file in the
::   XTE folder to have the wrong IP and the ICA client listener to actually
::   be listening on the wrong NIC.  To resolve this issue we will ensure 
::   group policy is applied then restart the IMASrv service *twice*.
::   We have to do it twice because the IMASrv won't recreate the httpd.conf
::   file on the first restart.
::
:: ===========================================================================================================

@ECHO OFF
CLS

SET COUNT=0

eventcreate /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script" /D "Starting Citrix_Restart_IMASrv_Delayed.cmd script"

del /q "C:\Program Files (x86)\Citrix\XTE\conf\httpd.conf"

ECHO N | GPUPDATE /FORCE >NUL
ping 127.0.0.1 -n 65 >NUL

:RetryCreate
net stop CitrixWMIService
net stop IMAService
net stop CitrixXTEServer

ping 127.0.0.1 -n 5 >NUL
net start IMAService
net start CitrixWMIService

ping 127.0.0.1 -n 5 >NUL
net stop CitrixWMIService
net stop IMAService

ping 127.0.0.1 -n 5 >NUL
net start IMAService
net start CitrixWMIService
net start CitrixXTEServer

IF %COUNT% GEQ 3 EXIT
SET /A COUNT=%COUNT%+1

IF NOT EXIST "C:\Program Files (x86)\Citrix\XTE\conf\httpd.conf" GOTO RetryCreate
eventcreate /ID 1 /L APPLICATION /T INFORMATION /SO "Local GP Startup Script" /D "Completed Citrix_Restart_IMASrv_Delayed.cmd script"</pre>
