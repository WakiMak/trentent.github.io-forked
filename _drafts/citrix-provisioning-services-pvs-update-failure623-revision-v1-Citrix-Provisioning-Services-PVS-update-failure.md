---
id: 1084
title: Citrix Provisioning Services (PVS) update failure
date: 2016-04-25T07:37:16-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/623-revision-v1/
permalink: /2016/04/25/623-revision-v1/
---
I recently got Event ID 0 on Citrix vDisk Update Service; &#8220;Update Task (UpdateTask) is not loaded&#8221;

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-yqDxvZxR6Nk/UmgPjRth25I/AAAAAAAAAZM/pCvvf_VKchI/s1600/1.PNG"><img src="http://3.bp.blogspot.com/-yqDxvZxR6Nk/UmgPjRth25I/AAAAAAAAAZM/pCvvf_VKchI/s320/1.PNG" width="320" height="181" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  The fix is to restart the Soap service on your chosen automatic update PVS server.
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-hvxBTD6a9ek/UmgPjyuLTvI/AAAAAAAAAZU/359xeUeOSzE/s1600/2.png"><img src="http://2.bp.blogspot.com/-hvxBTD6a9ek/UmgPjyuLTvI/AAAAAAAAAZU/359xeUeOSzE/s320/2.png" width="320" height="233" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->