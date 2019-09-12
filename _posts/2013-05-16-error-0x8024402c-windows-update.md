---
id: 645
title: ERROR 0x8024402c Windows Update
date: 2013-05-16T11:41:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/16/error-0x8024402c-windows-update/
permalink: /2013/05/16/error-0x8024402c-windows-update/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/error-0x8024402c-windows-update.html
blogger_internal:
  - /feeds/7977773/posts/default/6474214347093101944
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - Windows Update
---
Recently, I was applying Windows Update to a 2008 system and it failed on 4 updates after being successful for months.  I was unsure why, but the updates were Office updates.  I don't think that the fact they were Office updates are important, but it's something to mention anyways.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-ow6esL559Lo/UZUWuyngr-I/AAAAAAAAAPE/EaknDqhqZDk/s1600/2.PNG"><img src="http://1.bp.blogspot.com/-ow6esL559Lo/UZUWuyngr-I/AAAAAAAAAPE/EaknDqhqZDk/s320/2.PNG" width="320" height="226" border="0" /></a>
</div>

Symptoms of the issues I found and the resolution for this issue.

1) Getting "ERROR 8024402C" when running Windows Update.  
2) Checking %WINDIR%\WindowsUpdate.log reveals lines like:


```plaintext
2013-05-16 10:41:01:577 1404 820 DnldMgr BITS job {97BB86BA-69EA-4091-91E6-DBD1EE012652} hit a transient error, updateId = {E6EC40C4-CD27-4D9C-A8C2-CE2B8A31E903}.201, error = 0x80072EE7
2013-05-16 10:41:01:577 1404 820 DnldMgr BITS job {97BB86BA-69EA-4091-91E6-DBD1EE012652} hit a transient error, updateId = {E6EC40C4-CD27-4D9C-A8C2-CE2B8A31E903}.201, error = 0x80072EE7
2013-05-16 10:41:01:577 1404 10a0 AU   # WARNING: Download failed, error = 0x8024402C
2013-05-16 10:41:01:577 1404 10a0 AU   # WARNING: Download failed, error = 0x8024402C
```

To determine the cause of the issue, I used the nicely revamped Event Viewer and looked at the BITS-Client logs.  Which was a waste, nothing showed up there.  I checked the WindowsUpdateClient log and nothing was there either.  I then learned BITS uses WinHTTP when I was googling for this issue and saw there was a WinHTTP log file.  (You may have to enable analytics and debug logs).  I enabled the Diagnostics Log.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-CKU9V3u_rEI/UZUVf8EAdKI/AAAAAAAAAO4/Hjw0r12kKEw/s1600/1.PNG"><img src="http://1.bp.blogspot.com/-CKU9V3u_rEI/UZUVf8EAdKI/AAAAAAAAAO4/Hjw0r12kKEw/s1600/1.PNG" border="0" /></a>
</div>

When reattempting to execute Windows Update I went back into the log and scanned through it.  I found the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-vZjqn6OgVww/UZUXZP11HRI/AAAAAAAAAPQ/xyWrjOtBuSs/s1600/3.PNG"><img src="http://4.bp.blogspot.com/-vZjqn6OgVww/UZUXZP11HRI/AAAAAAAAAPQ/xyWrjOtBuSs/s320/3.PNG" width="320" height="109" border="0" /></a>
</div>

Windows update was going to the wrong server!  The event viewer said it was going to wswsus02.XXXX.ab.ca.  This was our old server address and we since replaced it with going directly to the IP of that server via GPO.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-nCtRBrQ017k/UZUYXD_UcRI/AAAAAAAAAPc/K5y8W5f4g18/s1600/4.PNG"><img src="http://1.bp.blogspot.com/-nCtRBrQ017k/UZUYXD_UcRI/AAAAAAAAAPc/K5y8W5f4g18/s320/4.PNG" width="320" height="109" border="0" /></a>
</div>

Checking regedit for the WU preferences showed it was pointing to the correct server, but for some reason Windows Update wasn't picking up the new server.  Rebooting the machine and refreshing the GPO did not resolve the issue.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-exb16dQwQiE/UZUYoaWQsaI/AAAAAAAAAPk/aljnxkvJj6E/s1600/5.PNG"><img src="http://2.bp.blogspot.com/-exb16dQwQiE/UZUYoaWQsaI/AAAAAAAAAPk/aljnxkvJj6E/s320/5.PNG" width="320" height="179" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is the correct settings
    </td>
  </tr>
</table>

Saman suggested some fixes:

net stop wuauserv  
net stop BITS  
net start wuauserv  
net start BITS  
wuauclt /resetauthorization /detectnow  
wuauclt /reportnow

These did not appear to work however.  But, we did try the following:  
esentutl /p %windir%securitydatabasesecedit.sdb /o  
Gpupdate /force

And I believe this worked.  After running this command, WinHTTP reported that it was pulling the Windows Update from the Microsoft servers, not our WSUS server:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-242IMIfTSi8/UZUZuynYz3I/AAAAAAAAAPw/WtG-Nc63Tj0/s1600/6.PNG"><img src="http://4.bp.blogspot.com/-242IMIfTSi8/UZUZuynYz3I/AAAAAAAAAPw/WtG-Nc63Tj0/s320/6.PNG" width="320" height="129" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      au.download.windowsupdate.com is not our WSUS server
    </td>
  </tr>
</table>

At this point I could have probably ran the net stop and net start commands and it may have worked, but I rebooted the server instead.

Upon the server coming back up I reran Windows Update and confirmed it was pulling from our WSUS server:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-VkfkanVVCJI/UZUaQjeEudI/AAAAAAAAAP4/ixj-TTwccog/s1600/7.PNG"><img src="http://4.bp.blogspot.com/-VkfkanVVCJI/UZUaQjeEudI/AAAAAAAAAP4/ixj-TTwccog/s320/7.PNG" width="320" height="127" border="0" /></a>
</div>

Success!

Windows Update then downloaded and installed the updates successfully!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->