---
id: 623
title: Citrix Provisioning Services (PVS) update failure
date: 2013-10-23T13:02:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/10/23/citrix-provisioning-services-pvs-update-failure/
permalink: /2013/10/23/citrix-provisioning-services-pvs-update-failure/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/10/citrix-provisioning-services-pvs-update.html
blogger_internal:
  - /feeds/7977773/posts/default/893816820057753969
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - PVS
---
I recently got Event ID 0 on Citrix vDisk Update Service; "Update Task (UpdateTask) is not loaded"

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