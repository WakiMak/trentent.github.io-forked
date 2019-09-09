---
id: 524
title: AppV5 - The trouble with AppV5 logs (and a solution!)
date: 2016-03-24T11:44:00-06:00
author: trententtye
layout: post
permalink: /2016/03/24/appv5-the-trouble-with-appv5-logs-and-a-solution/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/03/appv5-trouble-with-appv5-logs-and.html
blogger_internal:
  - /feeds/7977773/posts/default/7280956913840542108
image: /wp-content/uploads/2016/03/70.PNG-550x0-1.png
categories:
  - Blog
tags:
  - AppV
  - PowerShell
---
AppV5 introduced to us a slew of logs that made troubleshooting more difficult than it should have been.

For instance, say you get an error message:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="/wp-content/uploads/2016/03/70.PNG-550x0-1.png"><img src="/wp-content/uploads/2016/03/70.PNG-550x0-1-300x141.png" width="320" height="149" border="0" /></a>
</div>

How do you start debugging this?  Well, you could start by trying to decipher the error message AppV has [generated to a decimal value that you can then look at the first two digits to determine which 'part' of AppV generated](http://blogs.technet.com/b/gladiatormsft/archive/2013/11/13/app-v-on-operational-troubleshooting-of-the-v5-client.aspx) the error and you can examine that specific log.  Sounds kind of ridiculous when you spell it out.

Another thing you could do is you could [enable all logs just to be sure](http://www.applepie.se/app-v-5-enable-or-disable-all-debug-logs), but then you have to sift through the logs to find the time stamp that corresponds closest to the event that generated the message.  Sometimes it would be too far or close together or the debug logs generate so much information that your event viewer is just one big blob of 'the exact same time'.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="/wp-content/uploads/2016/03/1-4.png"><img src="/wp-content/uploads/2016/03/1-4-300x233.png" width="320" height="248" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      One of these could be the cause of my issue.  Going through them all is extremely cumbersome.
    </td>
  </tr>
</table>

Of course this isn't true that all these events are the same time, but it's all event viewer can display.  If you view the XML of the event you can see there is more precise time stamps, but regardless, trying to compare each event with other logs is cumbersome and very difficult.

Is there a better way?

Yes!  There is!  [And Microsoft has actually documented it here:](https://support.microsoft.com/en-us/kb/3037955)  
<span style="background-color: white; color: #333333; font-family: 'segoe ui' , 'lucida grande' , 'verdana' , 'arial' , 'helvetica' , sans-serif; font-size: 14px; line-height: 20.162px;"><a href="https://support.microsoft.com/en-us/kb/3037955">https://support.microsoft.com/en-us/kb/3037955</a></span>

This is a powershell script that generates a ETL file on your desktop then converts it to a text format.  The script is:

<pre class="lang:ps decode:true ">logman create trace AppVKB3037955Debug -o AppVDebug.ETL -cnf 01:00:00 -nb 10 250 -bs 16 -max 512 -ow -y
$providers = Get-WinEvent -ListProvider *Microsoft-Appv-* | Select-Object Name | Where-Object -Property Name -NotMatch "SharedPerformance"
foreach ($provider in $providers) { logman update trace AppVkb3037955Debug -p $($provider.Name) 0xe000000000ffffff 0x5 | Out-Null }
Logman Start AppVKB3037955Debug
 
cls
$nil = Read-Host "Please reproduce your issue now. Press [ENTER] when done."
 
Logman Stop AppVKB3037955Debug
dir *AppVDebug*.etl | foreach { netsh trace convert $_ overwrite=yes}
Logman Delete AppVKB3037955Debug</pre>

What does the output look like?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="/wp-content/uploads/2016/03/5-2.png"><img src="/wp-content/uploads/2016/03/5-2-300x106.png" width="320" height="113" border="0" /></a>
</div>

First thing to notice is that it's sorted chronologically, so if you can get a narrow time range to examine the error occurring it should be easier to spot.  Second thing is all event logs are added, and events themselves are included as they are generated.  So it should be easier to finding that specific component that causes the error.

If you are encountering AppV5 errors, this may be easier to help track down the error then trying to sift through the debug logs in event viewer.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->