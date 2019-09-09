---
id: 649
title: Citrix PVS bootup failure with the Boot Device Management ISO
date: 2013-05-01T16:16:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/01/citrix-pvs-bootup-failure-with-the-boot-device-management-iso/
permalink: /2013/05/01/citrix-pvs-bootup-failure-with-the-boot-device-management-iso/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/citrix-pvs-bootup-failure-with-boot.html
blogger_internal:
  - /feeds/7977773/posts/default/3810221960712950746
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - Provisioning Services
---
We had an issue recently with some Citrix Provisioning Servers (PVS) that were not getting a DHCP address when booting off of the Boot Device Management ISO (BDM.ISO). What was happening is the server would just show this:

<span style="font-size: x-small;"><span style="font-family: 'Courier New',Courier,monospace;">DHCP Discover ._._._._.</span></span>  
<span style="font-size: x-small;"><span style="font-family: 'Courier New',Courier,monospace;"><br /> </span></span><span style="font-size: x-small;"><span style="font-family: 'Courier New',Courier,monospace;">DHCP maximum number of retries reached.</span></span>

When booting off of PXE or a WinPE CD I was getting DHCP without issues.

When I did a Wireshark of the line I saw the BDM.ISO boot send out DHCPDISCOVER packets but it never received a response. I then Wiresharked the WinPE and PXE boots and saw the DHCPDISCOVER packet, followed by a DHCPOFFER, and so on. When I examined the two packets I saw the BDM.ISO DHCPDISCOVER packet actually was a BOOTP **unicast** whereas the PXE and WinPE packets were BOOTP **broadcast**. Thinking we had a DHCP Relay issue we checked our DHCP server (an InfoBlox server) and checked the logs for the MAC Address and here is what we saw when we booted with the BDM.ISO:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-hvcsVwGy5Yk/UYGH9Ez3QcI/AAAAAAAAAOA/aD8SkCs1aMI/s1600/1.jpg"><img src="http://3.bp.blogspot.com/-hvcsVwGy5Yk/UYGH9Ez3QcI/AAAAAAAAAOA/aD8SkCs1aMI/s320/1.jpg" width="320" height="61" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      BDM.ISO DHCP traffic
    </td>
  </tr>
</table>

The DHCP server was not responding to the DHCPDISCOVER. This only occurred with the unicast packet and for some reason was "load balance to peer". However it's setup, it appears UNICAST BOOTP packets are setup for load balancing but not sending a response.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-tYToYcCRXyg/UYGH-cOWuII/AAAAAAAAAOI/UpSyVoE_e1I/s1600/2.jpg"><img src="http://4.bp.blogspot.com/-tYToYcCRXyg/UYGH-cOWuII/AAAAAAAAAOI/UpSyVoE_e1I/s320/2.jpg" width="320" height="54" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      PXE/WinPE DHCP traffic
    </td>
  </tr>
</table>

The DHCP server is responding to a BROADCAST BOOTP packet in a very different way. There is no load balancing going on and the server responds to the DISCOVER packet.

Unfortunately, we did not resolve this issue when I wrote this. We got our farm to work by pointing the DHCP Relay to the previous DHCP server that is configured in such a way to resolve this DHCP request and present an DHCP OFFER. Hopefully our network guys will get the DHCP fixed on the proper server. If you are experiencing similar issues you may have a similar issue where the DHCP at your site is handling unicast DHCPDISCOVER packets differently then broadcast packets.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->