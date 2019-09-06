---
id: 591
title: AppV 5 - Unable to launch applications but other users can launch without issue
date: 2014-09-16T14:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/09/16/appv-5-unable-to-launch-applications-but-other-users-can-launch-without-issue/
permalink: /2014/09/16/appv-5-unable-to-launch-applications-but-other-users-can-launch-without-issue/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/09/appv-5-unable-to-launch-applications.html
blogger_internal:
  - /feeds/7977773/posts/default/4981585874277336632
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - Registry
---
We have AppV 5 SP2 HF4 installed on a Citrix PVS server with our AppV PackageInstallationRoot redirected to a static drive. Â I recently updated the PVS server than found my account could not launch AppV applications but other accounts could launch them without issue. Â I deleted my user account from the server, tried launching applications with /appvve (which appeared to work but didn't actually initialize the AppV environment) and procmon'ed the launch of the application. Â Procmon showed me the application actually launching, but immediately terminating.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-eMt3meSRgYE/VBiSKcTqbjI/AAAAAAAAAik/kK3OeQFgHio/s1600/1.png"><img src="http://4.bp.blogspot.com/-eMt3meSRgYE/VBiSKcTqbjI/AAAAAAAAAik/kK3OeQFgHio/s1600/1.png" width="320" height="210" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Bad Launch
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-YECiLgTtwns/VBiSRwGLMKI/AAAAAAAAAis/4PN2c0han9w/s1600/2.png"><img src="http://2.bp.blogspot.com/-YECiLgTtwns/VBiSRwGLMKI/AAAAAAAAAis/4PN2c0han9w/s1600/2.png" width="320" height="234" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Good Launch
    </td>
  </tr>
</table>

We can see that the application stops launching at the moment it initializes the virtual environment. Â Next we'll dive into the event logs and see if it gives us any more relevant information. Â I chose to enable a few event logs that deal with the early process of AppV5. Â I can't really tell you why I chose those ones, they are just a few that I've learned to monitor when I have problems launching apps.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-JBFoEO2kQk8/VBiTxzn0hDI/AAAAAAAAAi0/4CJKp0l6tJM/s1600/3.png"><img src="http://4.bp.blogspot.com/-JBFoEO2kQk8/VBiTxzn0hDI/AAAAAAAAAi0/4CJKp0l6tJM/s1600/3.png" width="114" height="320" border="0" /></a>
</div>

I enabled:  
Client-Integration  
Client-Orchestration  
Client-Vemgr  
Client-VirtualizationManager  
Subsystem-Venv

Launching the application gave me some error messages. Â From Client-VirtualizationManager:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-daWc4ONHF9U/VBiUo-yZI4I/AAAAAAAAAi8/Pr_Q3I9vPLs/s1600/4.png"><img src="http://2.bp.blogspot.com/-daWc4ONHF9U/VBiUo-yZI4I/AAAAAAAAAi8/Pr_Q3I9vPLs/s1600/4.png" width="320" height="225" border="0" /></a>
</div>

&nbsp;

<pre class="lang:default decode:true ">64 notification failed with error 5746736078216233004. Package {49bda935-0a17-43e5-aada-44858d95affa}, version {d506ff21-4e9a-40e5-b60f-94481ccbe76a}, pid 5176, ve id 0.
VirtualizationManager component failed to handle 46 activity. Error 4746865492684177448.</pre>

If we do the [backwards HRESULT dance](http://blogs.technet.com/b/gladiatormsft/archive/2013/11/13/app-v-on-operational-troubleshooting-of-the-v5-client.aspx) to try and get a useful error code we get this for the two errors:

<pre class="lang:ps decode:true">PS C:\Users\trententtye> (5746736078216233004).ToString("x")
4fc082040000002c

PS C:\Users\trententtye> (4746865492684177448).ToString("x")
41e0410400000028</pre>

According to the link, the object is 04 (

<table style="background-color: white; color: #2a2a2a; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 11.8181819915771px; line-height: 16.514181137085px; margin-left: 1px;" border="1" cellspacing="1" cellpadding="1">
  <tr>
    <td valign="top" width="101">
      <div align="center">
        04
      </div>
    </td>
    
    <td valign="top" width="365">
      Virtualization Manager
    </td>
    
    <td valign="top" width="313">
      Client-VirtualizationManager Log or Client-Vemgr Log
    </td>
  </tr>
</table>

)

with error code 0x28 and 0x2c. Â Googling those HRESULT codes reveals nothing. Â I then looked into Client-Vemgr and found some error messages there:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Bkv__cswE7I/VBiV8c4OL1I/AAAAAAAAAjE/NCfJBPZD6Og/s1600/5.png"><img src="http://4.bp.blogspot.com/-Bkv__cswE7I/VBiV8c4OL1I/AAAAAAAAAjE/NCfJBPZD6Og/s1600/5.png" width="320" height="70" border="0" /></a>
</div>

&nbsp;

<pre class="lang:default decode:true ">Request to generate mappings for globally-published package failed. package moniker 49BDA935-0A17-43E5-AADA-44858D95AFFA_D506FF21-4E9A-40E5-B60F-94481CCBE76A. group moniker . user sid S-1-5-21-38857442-2693285798-3636612711-15138285. result {Operation Failed}
The requested operation was unsuccessful..</pre>

Well, this is interesting. Â So it appears that AppV can't do mappings; which I assume are the CoW (Copy on Write) for the filesystem...? Â Procmon does not reveal anything with a access denied or failed to create directories, as a matter of fact it actually creates the %userprofile%\local\Microsoft\AppV\VFS directories. Â But it is specifically targeting my user account, as referenced by the SID.

At this point I scanned Procmon for my SID and found it existed in the AppV registry path here:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-rJFPDOzjXcg/VBiY5_bVsXI/AAAAAAAAAjM/1s0PEaqGyd8/s1600/6.png"><img src="http://3.bp.blogspot.com/-rJFPDOzjXcg/VBiY5_bVsXI/AAAAAAAAAjM/1s0PEaqGyd8/s1600/6.png" width="320" height="124" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Virtualization\LocalVFSSecuredUsers
    </td>
  </tr>
</table>

Procmon did not notice anything wrong (no access denied or such error messages). Â I then launched another application with a runas and saw it that a new registry key SID combination was dynamically generated. Â I then suspected that maybe the existence of this key maybe causing my issue... Â To verify I rebooted the PVS server and without using my account, checked and saw the registry key was there through reboots. Â My next test was to delete the key and try launching the application again.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-gaPfFVx_dNo/VBiaUVvx2_I/AAAAAAAAAjU/sPPPAOW_GQA/s1600/7.png"><img src="http://3.bp.blogspot.com/-gaPfFVx_dNo/VBiaUVvx2_I/AAAAAAAAAjU/sPPPAOW_GQA/s1600/7.png" width="320" height="158" border="0" /></a>
</div>

Lo and behold it worked! Â AppV 5 regenerated the key and launched the application. Â It appears if the key exists then it will fail to create the CoW mappings in the registry (which you can see in the screenshot when it's creating the MAV keys) and my issue is resolved.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->