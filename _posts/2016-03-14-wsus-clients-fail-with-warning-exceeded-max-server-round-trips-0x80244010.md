---
id: 526
title: 'WSUS clients fail with WARNING: Exceeded max server round trips: 0x80244010'
date: 2016-03-14T16:33:00-06:00
author: trententtye
layout: post
permalink: /2016/03/14/wsus-clients-fail-with-warning-exceeded-max-server-round-trips-0x80244010/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/03/wsus-clients-fail-with-warning-exceeded.html
blogger_internal:
  - /feeds/7977773/posts/default/5696956654271120031
image: /wp-content/uploads/2016/03/1-6.png
categories:
  - Blog
  - Uncategorized
tags:
  - Performance
  - Windows Update
---
[J C Hornbeck/Joe Tindale touched on this topic on a Microsoft blog post here.](https://blogs.technet.microsoft.com/sus/2008/09/18/wsus-clients-fail-with-warning-syncserverupdatesinternal-failed-0x80244010/)

In that post he touches on what&#8217;s actually happening:

<div style="background-color: white; border: 0px; box-sizing: inherit; color: #454545; font-family: WOL_Reg, 'Segoe UI', Tahoma, Arial, sans-serif; font-size: 14px; line-height: 21px; margin-bottom: 25px; outline: 0px; padding: 0px 0px 0px 30px; vertical-align: baseline; word-break: keep-all; word-wrap: break-word;">
  <span style="border: 0px; box-sizing: inherit; font-family: 'wol_bold' , 'segoe ui' , 'tahoma' , 'arial' , sans-serif; font-style: inherit; margin: 0px; outline: 0px; padding: 0px; vertical-align: baseline;"><u style="box-sizing: inherit;">Cause:</u></span>Â The error, 0x80244010, means WU_E_PT_EXCEEDED_MAX_SERVER_TRIPS and happens when a client has exceeded the number of trips allowed to a WSUS server.Â We have defined the <b><u>maximum number of trips as 200 within code and it cannot reconfigured</u></b>.Â A â€œtripâ€ to the server consist of the client going to the server and saying give me all updates within a certain scope.Â The server will give the client a certain number of updates within this trip based on the size of the update metadata.Â The server can send 200k worth of update metadata in a single trip so itâ€™s possible that 10 small updates will fit in that single trip.Â Other larger updates may require a single trip for each update if they exceed the 200k limit.Â Due to the way Office updates are published you are more likely to see this error if youâ€™re syncing Office updates since their metadata is typically larger in size.
</div>

<div>
  I&#8217;ve bolded the more important information. Â This is hardcoded and cannot be reconfigured. Â This, to me, is a bit ridiculous that it can&#8217;t be reconfigured.
</div>

<div>
</div>

<div>
  I <a href="https://gallery.technet.microsoft.com/scriptcenter/reset-windows-update-agent-d824badc">have a WSUS client where we &#8216;reset&#8217; the Windows Update client</a>. Â After resetting the client we were getting an error &#8220;WARNING: Exceeded max server round trips: 0x80244010&#8221;. Â We would try multiple times but this error wouldn&#8217;t go away and prevented us from running Windows Update on this system. Â So I started to investigate. Â The first thing I did was finish reading that blog entry. Â Hornbeck continues:
</div>

<div>
</div>

<div>
  <div style="background-color: white; border: 0px; box-sizing: inherit; color: #454545; font-family: WOL_Reg, 'Segoe UI', Tahoma, Arial, sans-serif; font-size: 14px; line-height: 21px; margin-bottom: 25px; outline: 0px; padding: 0px 0px 0px 30px; vertical-align: baseline; word-break: keep-all; word-wrap: break-word;">
    The client takes these new updates as they trickle down and inserts them into a small database to cache them for future use.Â <b>So during the first client synchronization with WSUS the client may get 75% of the available updates, put them into the database, <u>and then fail</u></b> at some point due to the number of max trips being exceeded.Â The good news is the <b><u>second synchronization cycle will not need to start from the beginning</u></b> since the client has already cached 75% of the updates into its database.Â <b><u>The second cycle will pick up where it left off and most likely finish getting all the updates from the server</u></b>.Â There have been a few rare cases where a third scan cycle is needed but more often than not two is sufficient.
  </div>
</div>

<div>
</div>

<div>
  Again, I have bolded and underlined the important parts.
</div>

<div>
</div>

<div>
  I started my investigation by trying to replicate the problem. Â I started up Windows Update and &#8216;checked for updates&#8217;.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-6.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-6-300x163.png" width="320" height="173" border="0" /></a>
</div>

<div>
</div>

<div>
  Ok, no problem. Â So I checked again.
</div>

<div>
</div>

<div>
</div>

<div>
  <div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-6.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-6-300x163.png" width="320" height="173" border="0" /></a>
  </div>
  
  <div>
  </div>
  
  <div>
    Well, Hornbeck/Tindale did say it may take a couple passes. Â Let&#8217;s try again.
  </div>
</div>

<div>
</div>

<div>
</div>

<div>
  <div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-6.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-6-300x163.png" width="320" height="173" border="0" /></a>
  </div>
  
  <div>
  </div>
</div>

<div>
  I&#8217;m getting worried now&#8230; Â Let&#8217;s try a fourth time.
</div>

<div>
</div>

<div>
</div>

<div>
  <div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-6.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-6-300x163.png" width="320" height="173" border="0" /></a>
  </div>
  
  <div>
  </div>
</div>

<div>
</div>

<div>
  Hmmm&#8230; Â At this point I wanted to better understand what Windows Update was doing. Â I originally installed Wireshark to trace the conversation but it was difficult and time consuming to try and count the traffic back and forth to the WSUS server. Â So I reverted my system <a href="https://www.telerik.com/download/fiddler/fiddler2">and installed Fiddler2</a> on it.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
</div>

<div>
</div>

<div>
</div>

<div>
  From the video you can actually see the traffic from the WSUS server. Â The request for the &#8216;Updates&#8217; starts at item 3 and completes at 203. Â Exactly 200 round trips.
</div>

<div>
</div>

<div>
  Since my previous Windows Update attempts at the WSUS server failed after a few tries I thought I would trace the traffic with Fiddler for the multiple attempts. Â My logic was I wanted to know if the traffic was &#8216;looping&#8217;; repeating itself and getting to the limit preventing updates. Â Or, would each send/receive be unique and thus, simply, more is needed?
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/03/2-3.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/2-3-254x300.png" width="270" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      The first bit of the first run. Â If the second run as identical or near identical traffic &#8216;packet sizes&#8217; I would be concerned it&#8217;s looping&#8230;
    </td>
  </tr>
</table>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/03/3-4.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/3-4-300x221.png" width="320" height="235" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      I reset Fiddler and started the second run. Â Completely different!
    </td>
  </tr>
</table>

<div>
  When I started the second run I was happy to see it was a completely different result. Â I cleared Fiddler for a 3rd time ran the &#8216;Check for Updates&#8217; until it timed out again, and cleared Fiddler again. Â I then thought to just let Fiddler capture everything. Â There really is no need to clear it each time. Â I monitored the Fiddler output looking for loops or patterns. Â The update check timed out a forth time (as before). Â There was no looping I could see.
</div>

<div>
</div>

<div>
  Finally, on the fifth run:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/4-2.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/4-2-300x205.png" width="320" height="219" border="0" /></a>
</div>

<div>
</div>

<div>
  Updates! Â We have updates!
</div>

<div>
</div>

<div>
  In the end, when the Microsoft blog post was written (2008) there probably was only enough updates that two or three passes would go through all of them. Â As time as gone on and more and more updates have been deployed to systems this hardcoded maximum is doing a huge disservice. Â Our Windows 2008R2 SP1 systems require <b>FIVE</b>Â passes of clicking/waiting/clicking/waiting/etc.
</div>

<div>
</div>

<div>
  A natural solution to this is to expose the &#8220;max server round trips&#8221; variable and allow it to be programmed by the organization according to their needs. Â The present state of this issue is <a href="https://www.google.ca/search?q=Exceeded+max+server+round+trips#">unnecessarily confusing</a> and arbitrarily limited.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->