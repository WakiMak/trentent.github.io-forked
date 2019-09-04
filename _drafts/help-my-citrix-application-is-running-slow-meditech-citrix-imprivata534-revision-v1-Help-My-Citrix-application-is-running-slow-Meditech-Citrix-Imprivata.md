---
id: 1154
title: Help! My Citrix application is running slow (Meditech, Citrix, Imprivata)
date: 2016-04-25T10:49:16-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/534-revision-v1/
permalink: /2016/04/25/534-revision-v1/
---
We have an application (Meditech) that users have reported as performing poorly in Citrix. &nbsp;They were able to confirm the poor performance by comparing it to a desktop that had the same software installed. &nbsp;I was brought on to try and understand these performance differences and why Citrix would be performing so much worse. &nbsp;To measure the performance, the user took me to a screen where they held down a key on the keyboard to increment a counter in the software. &nbsp;When holding the key down on the desktop the counter was quick and incremented at a steady pace, on Citrix it seemed OK at first but then slowed as time went on. &nbsp; To illustrate these performance differences, I made a video:

<div style="clear: both; text-align: center;">
</div>

So we definitely have a problem. &nbsp;My first steps on troubleshooting this problem was to compare the differences between the desktop and the VM. &nbsp;The VM had server processors but they were older, and at less GHz at 2.6GHz for the server. &nbsp;The desktop had 3.4GHz processors with a newer generation. &nbsp;If the processing is NOT latent sensitive then the faster processor&#8217;s can make a difference, and the CPUMark had them at 1500 and 2100 (~40% difference). &nbsp;At first glance, this seems like it could be the difference in our timings but it&#8217;s still too drastic at ~5s vs ~20s, a 400% difference. &nbsp;To try and narrow the difference I took the application to a completely bare server with no software on it what-so-ever and reran the test. &nbsp;It completed in about ~5-6s. &nbsp;The processor it ran on was comparable to the original server processor but ran at 2.7GHz instead. &nbsp;The processor was not running at nearly enough speed to make up the difference, something else must be consuming those cycles.

At this point I procmon&#8217;ed the benchmark and came up with the following:

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B10.54.50-2BPM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="70" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B10.54.50-2BPM-1-300x66.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      (PID 6660 is the process that the benchmark runs against)
    </td>
  </tr>
</table>

Very obviously, the pattern of each key stroke on the Citrix server is present with the initial pattern highlighted:

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B10.58.44-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="273" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B10.58.44-2BPM-1-300x256.png" width="320" /></a>
</div>

About 400ms from each key stroke to when the next one is registered. &nbsp;So what is delaying the 400ms? &nbsp;From my experience, unexplained delays are CPU related. &nbsp;I then looked at the process activity summary to see if I can find the bottleneck:

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.02.17-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="196" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.02.17-2BPM-1-300x184.png" width="320" /></a>
</div>

Again, very obviously we are seeing the CPU ramp up, and it can also explain the faster, initial iterations of the GUI as the CPU &#8216;ramps&#8217; up and doesn&#8217;t spike to the it&#8217;s maximum. &nbsp;None of the other graphs show any activity at the time of the benchmark so the suspect is highly on the CPU. &nbsp;When hovering over the graph we see the CPU percentage.

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.05.11-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="136" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.05.11-2BPM-1-300x128.png" width="320" /></a>
</div>

This is a 4 core box, so ~25% equals one full core for a single threaded application. &nbsp;Again, this points to the application being bottlenecked by the processor, but again, the difference is too large to consider just the CPU at this point. &nbsp;We need to find what part of the process is consuming these resources. &nbsp;Fortunately, ProcExp (process explorer) can help us determine what is going on within a process.

I started a new run and got properties of the process:

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.09.16-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="306" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.09.16-2BPM-1-300x288.png" width="320" /></a>
</div>

MGUI.DLL is consuming all of the CPU. &nbsp;That is a DLL utilized by the application, clicking on &#8216;Stack&#8217; gives us the hierarchy of commands being utilized by that thread.

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.12.28-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.12.28-2BPM-1-270x300.png" width="287" /></a>
</div>

From this, I can understand that ntoskrnl.exe, ntdll.dll are native Microsoft Windows functions, MGUI.DLL is utilized by Meditech, but what is ISXHook.dll?

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.14.33-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.14.33-2BPM-1-223x300.png" width="237" /></a>
</div>

Doing a search within the process shows that it&#8217;s utilized by Imprivata, a single sign-on solution we utilize to try and increase user efficiency. &nbsp;It works by &#8216;screen scraping&#8217; to determine fields that it needs to populate with user credentials to try and speed up user logins. &nbsp;Logically, this sounds like it could be causing the delay by screen scraping every time a key is stroked or a change is registered. &nbsp;To confirm this, we need to remove Imprivata from the application. &nbsp;Fortunately, it&#8217;s hooked in by services that can easily be terminated.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.18.18-2BPM-1.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="56" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.18.18-2BPM-1-300x53.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      I&#8217;m going to terminate everything that says &#8216;Imprivata Inc.&#8217;
    </td>
  </tr>
</table>

With the processes terminated I reran my test. &nbsp;Using Process Explorer and getting properties on the process, immediately CPU usage went from 25% down to 15% at a peak:

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.21.36-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="249" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.21.36-2BPM-1-300x233.png" width="320" /></a>
</div>

And getting Stack information showed a much cleaner stack:

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.23.37-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2015/12/Screen-2BShot-2B2015-12-18-2Bat-2B11.23.37-2BPM-1-203x300.png" width="216" /></a>
</div>

In conclusion, I may need to investigate into Imprivata to determine if I can reduce its polling rate or find some way of allowing it to &#8216;stop&#8217; polling the CSMagic process \*after\* the accelerated login. &nbsp;Its current settings (which I&#8217;m not familiar with, sadly) is not acceptable and causes a significant slow down. &nbsp;Fortunately, the root cause has been determined and we can work towards a full resolution.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->