---
id: 652
title: Reboot Server and PVS Streaming Service is started but the console shows the service is down
date: 2013-04-25T11:41:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/04/25/reboot-server-and-pvs-streaming-service-is-started-but-the-console-shows-the-service-is-down/
permalink: /2013/04/25/reboot-server-and-pvs-streaming-service-is-started-but-the-console-shows-the-service-is-down/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/04/reboot-server-and-pvs-streaming-service.html
blogger_internal:
  - /feeds/7977773/posts/default/684500711612849846
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - VMWare
  - VMWare tools
---
Upon rebooting a server we found the Citrix PVS Console showed the server as down. When we investigated the server we found the service was started and their were no errors in the logs that we could see. Restarting the service brought the server as up in the console. We did see one particular error though, the date was suddenly incorrect in the event viewer.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-w0Mp08326Nk/UXlozxiQ9qI/AAAAAAAAANk/YFMzDnuTUMI/s1600/1.jpg"><img src="http://1.bp.blogspot.com/-w0Mp08326Nk/UXlozxiQ9qI/AAAAAAAAANk/YFMzDnuTUMI/s320/1.jpg" width="296" height="320" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-jMwTGpq7UEk/UXlozmTQQVI/AAAAAAAAANc/MoHexK0T85A/s1600/2.jpg"> </a>
</div>

Further investigation showed EventID 52, the time service resync'ed a offset.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-jMwTGpq7UEk/UXlozmTQQVI/AAAAAAAAANg/WH1fHyjIx1g/s1600/2.jpg"><img src="http://2.bp.blogspot.com/-jMwTGpq7UEk/UXlozmTQQVI/AAAAAAAAANg/WH1fHyjIx1g/s320/2.jpg" width="320" height="222" border="0" /></a>
</div>

Since this was a virtual machine we checked the VMWare settings to confirm that the time was not being sync'ed

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-KbyRORCpIGg/UXlprFEwoaI/AAAAAAAAANw/EbgjE0qvmnY/s1600/3.jpg"><img src="http://2.bp.blogspot.com/-KbyRORCpIGg/UXlprFEwoaI/AAAAAAAAANw/EbgjE0qvmnY/s320/3.jpg" width="320" height="281" border="0" /></a>
</div>

But the time was still getting offset. Further investigation showed the VMWare hosts time was not set correctly and the server was having it's time set to the hosts time; even though the above check box was not set.

It appears VMWare has additional time synchronization settings that are enabled by default and must be set to explicitly deny to not have the time synchronize from different scenarios.

<http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1189>

Upon VMWare Tools starting on reboot, a "resume", or the tools being restarted or other scenarios. To prevent it from happening you must edit the VMX file and set the values as stated in the kb article above.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->