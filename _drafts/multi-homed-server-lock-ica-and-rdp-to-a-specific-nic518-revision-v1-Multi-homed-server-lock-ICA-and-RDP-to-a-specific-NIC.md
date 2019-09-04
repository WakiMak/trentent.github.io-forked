---
id: 1246
title: Multi-homed server, lock ICA and RDP to a specific NIC
date: 2016-04-25T12:04:13-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/518-revision-v1/
permalink: /2016/04/25/518-revision-v1/
---
<div style="clear: both; text-align: center;">
</div>

We implement a multi-homed setup with Citrix Provisioning Services. Â We have all of our production traffic on one NIC and all PVS traffic on a second nic. Â This helps us in troubleshooting when doing packet captures but does introduce other sets of challenges. Â One of these challenges is when I uninstalled and then installed updated VMWare tools on one of our vDisks it caused the NIC&#8217;s to renumber and reorder themselves. Â You&#8217;ve [probably read some articles](https://support.citrix.com/article/CTX136279) [saying to &#8216;show hidden devices&#8217; and uninstall any &#8216;ghost&#8217; devices](https://support.citrix.com/article/CTX133188); with a multi-homed setup this may not resolve your issue. Â My specific issue is my NIC&#8217;s now went from &#8220;0x1 and 0x2&#8221; to &#8220;0x3 and 0x4&#8221; in the [LANATABLE](https://support.microsoft.com/en-us/kb/924927). Â We apply a GPO to the ICA-TCP and RDP-TCP to force them to only utilize our &#8216;Production&#8217; NIC which we decided was going to be the second NIC.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/1-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/1-1-300x185.png" width="320" height="197" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Provision = 1st Nic, Production = 2nd Nic
    </td>
  </tr>
</table>

I uninstalled the ghost devices but because this change is all &#8216;in the registry&#8217; it wasn&#8217;t immediately noticeable by myself that the LANATABLE and NIC ordering had changed. Â I promoted my vDisk and then tried to RDP into it:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/04/2-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/2-1-300x119.png" width="320" height="127" border="0" /></a>
</div>

Well&#8230; Â I knew this wasn&#8217;t right. Â So I logged onto the server and checked the LANATABLE values:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/7-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/7-1-300x54.png" width="320" height="57" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Provision NIC
    </td>
  </tr>
</table>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/8-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/8-1-300x54.png" width="320" height="57" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Production NIC
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/3-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/3-1-300x22.png" width="320" height="23" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Provision NIC LanaID = 0x3 after the VMWare Tools upgrade
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/4-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/4-1-300x24.png" width="320" height="25" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <span style="font-size: 12.8px;">Production NIC LanaID = 0x4 after the VMWare Tools upgrade</span>
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/5-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/5-1-300x31.png" width="320" height="33" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      RDP targets LanAdapter 0x1 (!?)
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/6-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/6-1-300x24.png" width="320" height="25" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      ICA targets LanAdapter 0x2
    </td>
  </tr>
</table>

A couple issues popped out. Â The first was that the LanaID&#8217;s were wrong. Â I \*thought\* Provision should be #1 and Production should be #2, but we apply our LanAdapter ID&#8217;s via GPP so these values are correct for our other systems. Â I know both RDP and ICA need to be locked to the Production NIC and the order is \*correct\* but I&#8217;m a bit confused to why the numbers are different between RDP-TCP and ICA-TCP.

So I started on getting the issues resolved. Â First, I was going to resolve RDP. Â If it is targeting 0x1 that means that I need the Production NIC (VMWare Network Adapter #2) needs to be set as 0x1. Â So I edit the LanaId of the Production NIC to 0x1 and the Provision NIC to 0x2. Â I rebooted the box and I could RDP into it without issue. Â I then checked the Remote Desktop Session Host Configuration:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/9-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/9-1-239x300.png" width="254" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is the correct value.
    </td>
  </tr>
</table>

OK, so RDP is set correctly and works. Â I then tried to launch a Citrix application.

It failed.

It generated an event on the server:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/10-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/10-1-300x49.png" width="320" height="52" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Unable to connect to the CGP tunnel destination
    </td>
  </tr>
</table>

Looking at the ICA Listener configuration showed me the following:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/11-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/11-1-300x267.png" width="320" height="284" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      The value is wrong. Â It should be #2
    </td>
  </tr>
</table>

So the ICA-TCP listener was set to the &#8216;Provision&#8217; NIC. Â So our production traffic was not getting to it. This is the wrong value. Â My first thought was the LANATABLE would make sense here&#8230; Â We have the LANADAPTER key is set to 0x2 which would equal the &#8216;Provision&#8217; NIC under this configuraiton&#8230; Â So I changed the LANATABLE to be the reverse. Â 0x1 = Provision NIC and 0x2 = Production NIC.

The results:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td>
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/11-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/11-1-300x267.png" width="320" height="284" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="font-size: 12.8px;">
      <a style="font-size: medium; margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/04/12-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/12-1-300x216.png" width="320" height="230" border="0" /></a>
    </td>
  </tr>
</table>

Now both are wrong!

<div>
</div>

<div>
  Jeez.
</div>

<div>
</div>

<div>
  I reverted the LANATABLE to 0x1 = Production and 0x2 = Provision. Â Again, only the RDP-TCP connection changed. Â At this point the ICA Listener *must* be looking at another place&#8230; Â I used Procmon to trace the registry when I opened the ICA Listener Configuration and noticed it did NOT query LANATABLE but did go through and look and query this key:
</div>

<div>
</div>

<div>
  HKEY_LOCAL_MACHINESYSTEMCurrentControlSetservicesTcpipLinkage
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/04/13-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/13-1-300x125.png" width="320" height="133" border="0" /></a>
</div>

<div>
</div>

<div>
  The BIND order is set as Production (#2 NIC) first and Provision second in this order&#8230; Â To change this order you need to go to Advanced Settings and modify the connection order:
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/14-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/14-1-300x189.png" width="320" height="201" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Provision *should* be the top NIC here&#8230;
    </td>
  </tr>
</table>

<div>
  So I used the arrows on the side and moved Production to be second in this order:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/04/15-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/15-1.png" border="0" /></a>
</div>

<div>
</div>

<div>
  Rebooted and I checked the ICA Listener:
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/16-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/16-1-300x263.png" width="320" height="280" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Hurray!
    </td>
  </tr>
</table>

<div>
</div>

<div>
  I then tried both RDP and ICA traffic and both now worked correctly.
</div>

<div>
</div>

<div>
  So, the lesson learned here is that this registry key:
</div>

<div>
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td>
        <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/6-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/6-1-300x24.png" width="320" height="25" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="font-size: 12.8px;">
      </td>
    </tr>
  </table>
</div>

<div>
  Targets the BINDING order of the NIC&#8217;s.
</div>

<div>
</div>

<div>
  And this registry key:
</div>

<div>
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td>
        <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/04/5-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/04/5-1-300x31.png" width="320" height="33" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="font-size: 12.8px;">
      </td>
    </tr>
  </table>
  
  <p>
    Targets the LANATABLE and the values specified within.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->