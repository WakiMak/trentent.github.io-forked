---
id: 657
title: Slow Group Policy Client Side Extensions login with Windows 7
date: 2013-03-05T10:27:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/03/05/slow-group-policy-client-side-extensions-login-with-windows-7/
permalink: /2013/03/05/slow-group-policy-client-side-extensions-login-with-windows-7/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/03/slow-group-policy-client-side.html
blogger_internal:
  - /feeds/7977773/posts/default/5363519229579129692
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
  - Group Policy
---
We experienced an issue when we modified a GPO to include item-level filtering on an AD group.Â The issue was that Windows 7 machines with this GPO applied to where suddenly taking minutes to login.Â Windows XP machines, however, logged in almost instantly.

When going through the event logs for group policy on Windows 7 we were able to identify the CSE causing this issue.Â For us it was the "File processing extension".

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-9vdjd4qnNr0/UTYZ4brbgsI/AAAAAAAAALk/LCGQioziAjM/s1600/slow1.png"><img src="http://2.bp.blogspot.com/-9vdjd4qnNr0/UTYZ4brbgsI/AAAAAAAAALk/LCGQioziAjM/s320/slow1.png" width="320" height="126" border="0" /></a>
</div>

When we looked at the group policy we saw that the item-level filtering was filtering on a group with 11,000+ objects in it.Â We had two tasks in the GPO that were filtering on that group.Â When I attempted to open the group utilizing ActiveRoles Server (ARS) it was taking 40-50 seconds to populate each object in the group.Â I theorized that it appeared Windows 7 was iterating through each object like ARS was.Â To test this I installed Wireshark on the Windows 7 and XP machines and ran "GPUPDATE /FORCE".Â This triggered the CSE to execute.Â The following are the traces:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-Jf0wV2grIZ4/UTYbTHqfZOI/AAAAAAAAAL0/UERlIEuBveY/s1600/slow4.png"><img src="http://1.bp.blogspot.com/-Jf0wV2grIZ4/UTYbTHqfZOI/AAAAAAAAAL0/UERlIEuBveY/s400/slow4.png" width="400" height="277" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      XP Capture.Â It queries (highlighted) the group then continues on.
    </td>
  </tr>
</table>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-4Kl1pBGLLbE/UTYbIAT_zDI/AAAAAAAAALs/1R0yA5o47I8/s1600/slow3.png"><img src="http://3.bp.blogspot.com/-4Kl1pBGLLbE/UTYbIAT_zDI/AAAAAAAAALs/1R0yA5o47I8/s640/slow3.png" width="640" height="242" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Windows 7 Capture.Â It queries the group then all objects within the group.
    </td>
    
    <td style="text-align: center;">
    </td>
    
    <td style="text-align: center;">
    </td>
  </tr>
</table>

Obviously, with 11,000+ objects in the AD group Windows 7 will have a significantly slower logon if it's querying every object within the group.Â Fortunately, Microsoft has put out a fix for this:

## [<span style="font-weight: normal;"><span style="font-size: small;">You experience a long domain logon time in Windows Vista, Windows 7, Windows Server 2008 or Windows Server 2008 R2 after you deploy Group Policy preferences to the computer</span></span>](http://support.microsoft.com/default.aspx?scid=kb%3BEN-US%3B2561285)

So if you are experiencing slow login times with Windows 7 it maybe worth it to try this fix.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->