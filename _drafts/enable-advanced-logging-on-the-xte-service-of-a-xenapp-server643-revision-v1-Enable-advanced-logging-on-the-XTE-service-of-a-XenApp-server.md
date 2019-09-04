---
id: 1106
title: Enable advanced logging on the XTE service of a XenApp server
date: 2016-04-25T08:12:52-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/643-revision-v1/
permalink: /2016/04/25/643-revision-v1/
---
Back in February or March we did Windows updates on our PVS XenApp servers and then sometime after the servers do not allow anyone to login.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-qlV0tbFkNe4/UZ5Rfav2COI/AAAAAAAAAQo/rkIRkB5Y_Vs/s1600/1.PNG"><img src="http://2.bp.blogspot.com/-qlV0tbFkNe4/UZ5Rfav2COI/AAAAAAAAAQo/rkIRkB5Y_Vs/s320/1.PNG" width="320" height="52" border="0" /></a>
</div>

The issue can crop up as a CGP Tunnel error message, a protocol driver error message or something along those lines. Â We do not know why it&#8217;s happening or why it only happens after we apply Windows updates from that time period. Â The odd thing is it&#8217;s intermittent as well, we can launch 20 systems from 1 vDisk that has the updates applied and everything will be fine for 2 weeks then, suddenly, 5 of the systems won&#8217;t allow logins via ICA with the errors. Â Rebooting the servers sometimes fixes it, sometimes not. Â Very intermittent and very weird. Â So I attempted to troubleshoot this issue again by adding:

<pre class="lang:default decode:true "># Log Level
loglevel debug</pre>

to the httpd.conf in the XTE folder of a server that was exhibiting these issues. Â The log levels are listed here:  
<http://support.citrix.com/article/CTX114680>

After adding those lines and restarting the XTE service the issue resolved itself! Â Frustrating to be sure, and I will look at adding this line to our vDisk image so that when it crops up we&#8217;ll have more diagnostic data to look at.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->