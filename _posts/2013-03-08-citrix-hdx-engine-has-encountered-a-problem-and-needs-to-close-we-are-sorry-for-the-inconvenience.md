---
id: 655
title: Citrix HDX Engine has encountered a problem and needs to close we are sorry for the inconvenience.
date: 2013-03-08T12:13:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/03/08/citrix-hdx-engine-has-encountered-a-problem-and-needs-to-close-we-are-sorry-for-the-inconvenience/
permalink: /2013/03/08/citrix-hdx-engine-has-encountered-a-problem-and-needs-to-close-we-are-sorry-for-the-inconvenience/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/03/citrix-hdx-engine-has-encountered.html
blogger_internal:
  - /feeds/7977773/posts/default/3722250098076508292
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Receiver
  - Performance
---
The scourage of many a Citrix tech.

Citrix HDX Engine has encountered a problem and needs to close we are sorry for the inconvenience.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Io7fn5OW6bk/UTokx4eZygI/AAAAAAAAAME/7qEOR-X8AFk/s1600/1.png"><img src="http://4.bp.blogspot.com/-Io7fn5OW6bk/UTokx4eZygI/AAAAAAAAAME/7qEOR-X8AFk/s320/1.png" width="320" height="152" border="0" /></a>
</div>

Numerous forum posts that I've seen without a solution.Â I have encountered this across two companies and have encountered the same solution both times.

**Symptoms**:

1) When you click Debug you get this information

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-xnUMakv9xiQ/UTolDXv2zaI/AAAAAAAAAMM/sVhxuKws3WM/s1600/2.png"><img src="http://4.bp.blogspot.com/-xnUMakv9xiQ/UTolDXv2zaI/AAAAAAAAAMM/sVhxuKws3WM/s320/2.png" width="320" height="80" border="0" /></a>
</div>

AppName: wifca32.exe  
ModName: msvcr80.dll

2) Event viewer shows "Faulting application wfica32.exe..." "faulting module msvcr80.dll..."

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-CLkAt1l4ViQ/UTolRaE9HxI/AAAAAAAAAMU/UPzEzQw94CE/s1600/3.png"><img src="http://2.bp.blogspot.com/-CLkAt1l4ViQ/UTolRaE9HxI/AAAAAAAAAMU/UPzEzQw94CE/s320/3.png" width="283" height="320" border="0" /></a>
</div>

3) The error dialog occurs after an application is launched (maybe between 10 seconds to 120 seconds afterwards).Â The user can move the dialog out of the way and continue working without issue, however clicking the "Close" button will terminate the application.Â Sometimes though, the error occurs before the application is fully launched.

**Background**:

The [Citrix client opens virtual channels](http://support.citrix.com/article/CTX116890) as it connects to the server.

**Overview of client-server data exchange using a virtual channel.**  
1. The client connects to the & Server. The client passes information about the virtual channels it supports to the server.  
2. The server-side application starts, obtains a handle to the virtual channel, and optionally queries for additional information about the channel.  
3. The client virtual driver and server-side application pass data using the following two methods:  
If the server application has data to send to the client, the data is sent to the client immediately. When the data is received by the client, the WinStation driver de-multiplexes the virtual channel data from the ICA stream and immediately passes it to the client virtual driver.  
If the client virtual driver has data to send to the server, the data is sent the next time the WinStation driver polls it. When the data is received by the server, it is queued until the virtual channel application reads it. There is no way to alert the server virtual channel application that data was received.  
4. When the server virtual channel application is finished, it closes the virtual channel and frees any allocated resources.

**Issue:**  
If your application starts and the dialog box appears afterwards; we can conclude one of the virtual channels has crashed.Â Usually, if this scenario appears it's because your application has "Don't wait for printers".Â If your application does not have this checkbox then sometimes the application will crash before the application is loaded.Â With this knowledge we have narrowed down our culprit.Â Printers.Â An example of a printer list with a client that was having this issue:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-Xk5RK9S3fKU/UToodHayXYI/AAAAAAAAAMc/h1kqcUwi5EI/s1600/4.png"><img src="http://3.bp.blogspot.com/-Xk5RK9S3fKU/UToodHayXYI/AAAAAAAAAMc/h1kqcUwi5EI/s320/4.png" width="320" height="269" border="0" /></a>
</div>

We have a Citrix policy to only map the default printer, but during the virtual channel creation, all printers become connected to the server.Â I was able to verify this with procmon; watching as it iterated through the registry keys for each printer.

A simple test to determine if a bad printer is causing your issue is to disable the Print Spooler service:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-4TeHS0GW2zk/UTopAUG5dkI/AAAAAAAAAMk/_QK6DGHsr1w/s1600/5.png"><img src="http://3.bp.blogspot.com/-4TeHS0GW2zk/UTopAUG5dkI/AAAAAAAAAMk/_QK6DGHsr1w/s320/5.png" width="262" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Click St<u>o</u>p
    </td>
  </tr>
</table>

After stopping the service and terminating any existing sessions, relaunch the application.Â If you no longer get an error (as in my case) then the issue is during the virtual channel creation of one of the faulty printers.Â I cleaned up the printers that the user had, removing all non-needed ones and they did not encounter the error message afterwards.Â There issue was resolved.Â I have seen that cleaning up a printer queue is sometimes not enough and the printers need to be deleted and recreated.Â I've yet to encounter a printer that I've recreated that has caused the issue to persist, but I guess it's possible.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-ERkuGG_7ZQM/UTop4BK1dZI/AAAAAAAAAMs/weqas-9WFgw/s1600/6.png">Â </a>
</div>

In the example above after deleting the users printers and restarting the print spooler the printers came back.Â The user did not have permission to delete the printers from the HKLM so I needed to do so manually.

<div style="text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-ERkuGG_7ZQM/UTop4BK1dZI/AAAAAAAAAMs/weqas-9WFgw/s1600/6.png"><img src="http://3.bp.blogspot.com/-ERkuGG_7ZQM/UTop4BK1dZI/AAAAAAAAAMs/weqas-9WFgw/s320/6.png" width="320" height="261" border="0" />Â </a>
</div>

So ensure you test restarting the printer spooler and see if the printer comes back to the user to ensure the user has appropriate rights to remove the printer.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->