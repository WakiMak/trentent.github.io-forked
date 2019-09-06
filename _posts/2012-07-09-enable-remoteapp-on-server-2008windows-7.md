---
id: 675
title: Enable RemoteApp on Server 2008/Windows 7
date: 2012-07-09T09:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/07/09/enable-remoteapp-on-server-2008windows-7/
permalink: /2012/07/09/enable-remoteapp-on-server-2008windows-7/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/07/enable-remoteapp-on-server-2008windows.html
blogger_internal:
  - /feeds/7977773/posts/default/651109431041942128
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
---
<div>
  We have two Citrix farms, a & 5 farm and a & 6 farm. &nbsp;& 6 only supports 64bit OS's.
</div>

<div>
</div>

We have some applications that will not operate on a 64bit OS (Microsoft FRx). &nbsp;Since we want to decommission our old Citrix & 5 farm because we're moving to & 6/6.5 we need a solution. &nbsp;The solution I have come up with is to use Microsoft RemoteApp functionality to publish this application through & 6. &nbsp;I've come [across this blog post that details how to do it](http://geekswithblogs.net/twickers/archive/2009/12/18/137048.aspx), but I'm going to summarize the technical changes here:

<div>
</div>

<div>
  <strong style="color: #444444; font-family: Arial, Verdana, Tahoma; font-size: small;">Step 1</strong>
</div>

<div>
  <div style="color: #444444; font-family: Arial, Verdana, Tahoma; font-size: small;">
    Run regedit (registry editor) and locate the key&nbsp;<strong>TsAppAllowList</strong>
  </div>
  
  <div style="color: #444444; font-family: Arial, Verdana, Tahoma; font-size: small;">
    &nbsp;&nbsp;&nbsp;&nbsp; a)&nbsp;<em>New Key</em>,&nbsp;<strong>Applications</strong>.<br />&nbsp;&nbsp;&nbsp;&nbsp; b) Under&nbsp;<strong>Applications</strong>, create&nbsp;<em>New Key</em>,&nbsp;<strong>1234567</strong>&nbsp;(the key name is not important, we just need&nbsp;<em>any</em>&nbsp;key for next two steps)<br />&nbsp;&nbsp;&nbsp;&nbsp; c) In the new key,&nbsp;<em>Create New -> String Value</em>,&nbsp;<strong>Name</strong>. Set value to&nbsp;<strong>Notepad</strong><br />&nbsp;&nbsp;&nbsp;&nbsp; d) Also in the new key,&nbsp;<em>Create New -> String Value</em>,&nbsp;<strong>Path</strong>. Set to&nbsp;<strong>c:windowssystem32Notepad.exe</strong>
  </div>
  
  <div style="color: #444444; font-family: Arial, Verdana, Tahoma; font-size: small;">
  </div>
  
  <p>
    Navigate back to the&nbsp;<strong>TsAppAllowList</strong><strong>&nbsp;</strong>branch<br />&nbsp;&nbsp;&nbsp;&nbsp; a) Edit&nbsp;<strong>fDisableAllowList</strong>&nbsp;value, and set to&nbsp;<strong>1</strong><br /><strong><br /></strong><br /><strong>Step 2 – Creating the RDP file to access the RemoteApp</strong><br />Now the guest operating system has a RemoteApp created we need to use a Remote Desktop Connection to access that application.<br />1. Run remote desktop connection, setup your desired settings as you would in a normal connection<br />2. Save the settings to an RDP file.<br />&nbsp;<br />3) Use Notepad to open the RDP&nbsp;file to edit the configuration file,<br />&nbsp;&nbsp; a) Modify the setting;&nbsp;<strong>remoteapplicationmode:i:0</strong>&nbsp; to&nbsp;&nbsp;<strong>remoteapplicationmode:i:1</strong><br />&nbsp;&nbsp; b) Add the setting;&nbsp;<strong>remoteapplicationprogram:s:Notepad</strong><br />&nbsp;&nbsp; c) Add the setting;&nbsp;<strong>disableremoteappcapscheck:i:1</strong><br />&nbsp;&nbsp; d) Add the setting;&nbsp;<strong>alternate shell:s:rdpinit.exe</strong><br />&nbsp;&nbsp; e) Save the RDP file
  </p>
  
  <p>
    <strong>Final thought</strong><br />Similar to VirtualBox seamless mode, you will not be able to move the floating guest application window between monitors, unless you save the RDP to use all monitors available.&nbsp; Either set this option prior to saving the RDP file, or edit the RDP setting&nbsp;<strong>multimon:i:1</strong>.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->