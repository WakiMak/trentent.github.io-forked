---
id: 632
title: 'â€œAn error occurred while making the requested connectionâ€ &#8211; Citrix Web Interface'
date: 2013-07-22T16:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/07/22/an-error-occurred-while-making-the-requested-connection-citrix-web-interface/
permalink: /2013/07/22/an-error-occurred-while-making-the-requested-connection-citrix-web-interface/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/07/an-error-occurred-while-making.html
blogger_internal:
  - /feeds/7977773/posts/default/6500702903207160786
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - Web Interface
---
So I&#8217;m getting the dreadedÂ â€œAn error occurred while making the requested connectionâ€ while trying to launch some applications from our Citrix Web Interface. Â It started happening suddenly but I&#8217;m tasked with figuring out why. Â First thing I did was go to the Web Interface and check the event logs. Â I found the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-gVBe3-2VOB8/Ue2suwbCs9I/AAAAAAAAAXU/C8OJsFChWWI/s1600/30102.png"><img src="http://2.bp.blogspot.com/-gVBe3-2VOB8/Ue2suwbCs9I/AAAAAAAAAXU/C8OJsFChWWI/s320/30102.png" width="320" height="108" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-8JYx_34Jn6A/Ue2swOqP2EI/AAAAAAAAAXc/Ag-lU_GbWhY/s1600/30102-details.png"><img src="http://4.bp.blogspot.com/-8JYx_34Jn6A/Ue2swOqP2EI/AAAAAAAAAXc/Ag-lU_GbWhY/s320/30102-details.png" width="320" height="158" border="0" /></a>
</div>

This wasn&#8217;t much help, but I was able to narrow down that this was happening on one set of our servers that are split across two DC&#8217;s. Â One set of servers at BDC was fine, the other set of servers at ADC had a subset of servers that were not. Â Doing a qfarm /load showed the problematic servers had no users on them at all, and no load evaluators were applied that would be causing our issue.

Logging into the server it was deteremined that it&#8217;s DNS was registered to the wrong NIC (it was a PVS server that was multi-homed) and even worse for some of the servers, the NIC IP address was an old address and the new address wasn&#8217;t even resolving!

For some reason it now appears our Windows 2008 servers are not registering their DNS on startup. Â To resolve this issue for us we added a startup script with the simple command &#8220;ipconfig /registerdns&#8221; and within a few seconds the IP address is registered within DNS correctly and with the correct NIC. Â We suspect that something is misconfigured at ADC as BDC does not have this issue nor does it need this tweak, but this is our work around until that is resolved.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->