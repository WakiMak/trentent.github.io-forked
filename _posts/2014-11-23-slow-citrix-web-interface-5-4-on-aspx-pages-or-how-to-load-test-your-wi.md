---
id: 586
title: Slow Citrix Web Interface 5.4 on ASPX pages (or how to load test your WI)
date: 2014-11-23T23:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/11/23/slow-citrix-web-interface-5-4-on-aspx-pages-or-how-to-load-test-your-wi/
permalink: /2014/11/23/slow-citrix-web-interface-5-4-on-aspx-pages-or-how-to-load-test-your-wi/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/11/slow-citrix-web-interface-54-on-aspx.html
blogger_internal:
  - /feeds/7977773/posts/default/5910316457338326485
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - Web Interface

---
We've been having issues with our Citrix Web Interface (5.4.2) with it cratering to the point that you cannot even get to the explicit logon page, stuck on SilentDetection.aspx or another aspx page.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-wuYRQVOpfgE/VG_t3eMkCZI/AAAAAAAAAoI/JGU4oMdEsc8/s1600/one_moment_please.png"><img src="http://2.bp.blogspot.com/-wuYRQVOpfgE/VG_t3eMkCZI/AAAAAAAAAoI/JGU4oMdEsc8/s1600/one_moment_please.png" width="640" height="256" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      One Moment Please...  This screen takes minutes to resolve
    </td>
  </tr>
</table>

So what could be causing this?  More often than not, it's a broken XML broker that isn't responding in a reasonable amount of time.  But XML brokers don't come into play with 'Explicit Logon', especially if you can't even get to the logon page.  In our scenario, we have two production Citrix Web Interface servers which are load-balanced in a round-robin scenario.

So I theorized that what's happening is we are experiencing a load issue.  That is our server cannot handle the number of connections that we our clients and PNA agents are producing.  How can we prove this out?  How many connections can our web interface accept?

To test out what the maximum number of connections our web interface servers can sustain I came up with a plan.  We need to thoroughly exercise the ASP.NET scripts that Citrix has put together for the web interface.  To do that we need I decided that logging in via explicit logon, skipping client installation (so client detection occurs) and then application enumeration.  This sequence hits the following ASPX pages:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-a9u9u-kAe2U/VHJMNxuHOUI/AAAAAAAAAoY/A2jgAc8v3JA/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B2.05.13%2BPM.png"><img src="http://3.bp.blogspot.com/-a9u9u-kAe2U/VHJMNxuHOUI/AAAAAAAAAoY/A2jgAc8v3JA/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B2.05.13%2BPM.png" width="640" height="430" border="0" /></a>
</div>

&nbsp;

<div>
</div>

<div>
  The timeline of processing the aspx pages of a unloaded server looks like this:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-IP5Jh6Im7AY/VHJMxHQs0-I/AAAAAAAAAog/_LLZGDOCBJk/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B2.04.16%2BPM.png"><img src="http://1.bp.blogspot.com/-IP5Jh6Im7AY/VHJMxHQs0-I/AAAAAAAAAog/_LLZGDOCBJk/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B2.04.16%2BPM.png" width="640" height="368" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  The initial silentDetection.aspx's take the majority of the processing time, about 4.3 seconds.  To create the ability to simulate this load, Microsoft has a tool called "WCAT", or the <a href="http://technet.microsoft.com/en-us/magazine/2008.04.utilityspotlight.aspx">Web Capacity Analysis Tool</a>.  This tool allows you to setup "scenario's" which are a sequence of HTTP actions (GET/POST/etc) which simulates each step of the sequence above.  To record this sequence for WCAT, you need to install Fiddler 2 then install the <a href="http://fiddler2wcat.codeplex.com/releases/view/35356">WCAT scenario creator</a>.  There are numerous WCAT scenario creators, but I used this one.  To create the scenario do the following:
</div>

<div>
</div>

<div>
</div>

<div>
  1) Launch Fiddler 2 and click 'Launch'
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-zY3-0PDr8gc/VHJZ-HlQh5I/AAAAAAAAAow/Xb32zcp-frY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.00.40%2BPM.png"><img src="http://1.bp.blogspot.com/-zY3-0PDr8gc/VHJZ-HlQh5I/AAAAAAAAAow/Xb32zcp-frY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.00.40%2BPM.png" width="320" height="45" border="0" /></a>
</div>

<div>
</div>

<div>
  2) Go through your login sequence on your web server until you hit the page with application enumerations.
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-JElmxCpRJZk/VHJaYc6tQ4I/AAAAAAAAAo8/v7xJk5aIsh0/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.14%2BPM.png"><img src="http://4.bp.blogspot.com/-JElmxCpRJZk/VHJaYc6tQ4I/AAAAAAAAAo8/v7xJk5aIsh0/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.14%2BPM.png" width="320" height="250" border="0" /></a>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-_xO4jzeY5gA/VHJaYamTLqI/AAAAAAAAApA/dTUEorgdGoA/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.33%2BPM.png"><img src="http://4.bp.blogspot.com/-_xO4jzeY5gA/VHJaYamTLqI/AAAAAAAAApA/dTUEorgdGoA/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.33%2BPM.png" width="320" height="301" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Wp5aguDailI/VHJaYQmfSEI/AAAAAAAAAo4/ESt4misvaoY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.44%2BPM.png"><img src="http://1.bp.blogspot.com/-Wp5aguDailI/VHJaYQmfSEI/AAAAAAAAAo4/ESt4misvaoY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.01.44%2BPM.png" width="320" height="305" border="0" /></a>
</div>

&nbsp;

<div>
</div>

<div>
  3) Go back to Fiddler 2 and at the bottom of the screen on the left in the black bar, type "wcat reset"
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-SgfK9mh10AI/VHJa6m8HAUI/AAAAAAAAApQ/N544tPlu1qk/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.12%2BPM.png"><img src="http://2.bp.blogspot.com/-SgfK9mh10AI/VHJa6m8HAUI/AAAAAAAAApQ/N544tPlu1qk/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.12%2BPM.png" width="203" height="320" border="0" /></a>
</div>

<div>
</div>

<div>
  4) Go Edit > Select All.  Then in the black bar, type 'wcat addtrans'
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Mf4Ia7eppZQ/VHJbKa3gICI/AAAAAAAAApY/5U8G9Y9EjdY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.02.15%2BPM.png"><img src="http://1.bp.blogspot.com/-Mf4Ia7eppZQ/VHJbKa3gICI/AAAAAAAAApY/5U8G9Y9EjdY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.02.15%2BPM.png" width="207" height="320" border="0" /></a>
</div>

<div>
</div>

<div>
  5) Lastly, type 'wcat save'.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-hSGFvjxj4lg/VHJbgt0hQNI/AAAAAAAAApg/SNP3krUJzhg/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.30%2BPM.png"><img src="http://1.bp.blogspot.com/-hSGFvjxj4lg/VHJbgt0hQNI/AAAAAAAAApg/SNP3krUJzhg/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.30%2BPM.png" width="320" height="122" border="0" /></a>
</div>

<div>
</div>

<div>
  This will create a fiddler.wcat file in your C:Program Files (x86)Fiddler2 directory.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-6QnVOlzcBoU/VHJb7f_E2GI/AAAAAAAAApo/_jGnxn5UKaI/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.46%2BPM.png"><img src="http://2.bp.blogspot.com/-6QnVOlzcBoU/VHJb7f_E2GI/AAAAAAAAApo/_jGnxn5UKaI/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.03.46%2BPM.png" width="221" height="320" border="0" /></a>
</div>

<div>
</div>

<div>
  You can now open this file as a text file to see the scenario you just created.  One of the issues with this WCAT scenario creator you will need to fix up some cookie values.  Some cookie values are captured exactly as described, which has some issues as double quotes need to be escaped in the scenario file.
</div>

<div>
</div>

<div>
  So, for example, this line:
</div>

<div>
  <pre class="lang:ps decode:true ">value = "WIUser="CTX_ForcedClient#Off~CTX_LaunchMethod#Ica-Local~CTX_AuthMethod#Explicit~CTX_CurrentFolder#%7bAllResources%3d%5c%7d~CTX_ViewStyle#%7bAllResources%3dIcons%7d"; WINGDevice="CTX_DeviceId#WI_YxJYRT6LZxvCnDWTF"";</pre>
</div>

<div>
</div>

<div>
  Won't work.  You need to escape out the double-quotes with a back-slash so it looks like so (the backslashes are bolded and underlined):
</div>

<div>
</div>

<div>
  <pre class="lang:ps decode:true ">value = "WIUser=\"CTX_ForcedClient#Off~CTX_LaunchMethod#Ica-Local~CTX_AuthMethod#Explicit~CTX_CurrentFolder#%7bAllResources%3d%5c%7d~CTX_ViewStyle#%7bAllResources%3dIcons%7d\"; WINGDevice=\"CTX_DeviceId#WI_YxJYRT6LZxvCnDWTF\"";</pre>
</div>

<div>
</div>

<div>
  Once you've escape'd the double quotes, save the file as 'scenario.ubr'; you can now setup <a href="http://technet.microsoft.com/en-us/magazine/2008.04.utilityspotlight.aspx">WCAT</a>.
</div>

<div>
</div>

<div>
  Install WCAT on some test boxes or a single box (for the purposes of this demo I'm doing just a single box).
</div>

<div>
</div>

<div>
  Copy over your scenario.ubr to the C:Program Fileswcat folder.  I took the settings.ubr file from the Samples directory in the wcat folder and put it in the root of the wcat folder as well.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-_NJUDtIzEIA/VHJi_Tl7NqI/AAAAAAAAAp4/PMJvBt7a05I/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.42.50%2BPM.png"><img src="http://3.bp.blogspot.com/-_NJUDtIzEIA/VHJi_Tl7NqI/AAAAAAAAAp4/PMJvBt7a05I/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B3.42.50%2BPM.png" width="400" height="183" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  You then need to start the controller and attach clients to this server.  Open a command prompt to the wcat directory.  The command line I used for the controller is:
</div>

<div>
  wcctl -t scenario.ubr -f settings.ubr -s wsctxwi01t -c 20 -v 20 -w 300 -u 300 -n 100
</div>

<div>
</div>

<div>
  the values are:
</div>

<div>
  -c = # of clients to connect to this controller.
</div>

<div>
  -v = # of virtual clients to launch per client
</div>

<div>
  -w = warm up time before all clients are connected
</div>

<div>
  -u = duration of testing
</div>

<div>
  -n = cooldown period for clients to disconnect.
</div>

<div>
</div>

<div>
  Since I'm testing from one computer I then used the following command to start all my clients:
</div>

<div>
</div>

<div>
  for /l %A IN (1,1,20) do (start "" wcclient.exe localhost)
</div>

<div>
</div>

<div>
  The 20 clients connect and start loading the server, following the sequence we recorded earlier.
</div>

<div>
</div>

<div>
  If you want to monitor to see where your maximum user load is vs. maximum performance (e.g., near instant speed), go to your web interface server and create a data collector set with the following counters:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-bTGCBwrD7R8/VHJsNKif0GI/AAAAAAAAAqI/Uvfo2psexfU/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B4.22.06%2BPM.png"><img src="http://4.bp.blogspot.com/-bTGCBwrD7R8/VHJsNKif0GI/AAAAAAAAAqI/Uvfo2psexfU/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B4.22.06%2BPM.png" width="320" height="76" border="0" /></a>
</div>

<div>
</div>

<div>
  Without any clients connected, start your data collector, then start the WCAT load testing.
</div>

<div>
</div>

<div>
  This is what I see with regards to our load:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-zFB2ShrLg9E/VHKzYTc9-dI/AAAAAAAAAqY/Y0KZUKnASzQ/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.25.25%2BPM.png"><img src="http://3.bp.blogspot.com/-zFB2ShrLg9E/VHKzYTc9-dI/AAAAAAAAAqY/Y0KZUKnASzQ/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.25.25%2BPM.png" width="640" height="418" border="0" /></a>
</div>

<div>
</div>

<div>
  You can see around 220 current connections the Requests in Application Queue shoots up.  At this point any additional requests to ASPX files goes into a queue that makes the website appear 'frozen' which it waits for the ASPX file to be processed and sent down to you.  Citrix attempts to 'hide' this page by loading the 'loading.htm' during ASPX processing.  This page is your 'One Moment Please' page.  If you show the Request Execution Time counter, we can see the longest time taken to process a ASPX page:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-fGzXNxab2mQ/VHK1icsb2CI/AAAAAAAAAqk/0jxIBKWJfEY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.34.58%2BPM.png"><img src="http://2.bp.blogspot.com/-fGzXNxab2mQ/VHK1icsb2CI/AAAAAAAAAqk/0jxIBKWJfEY/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.34.58%2BPM.png" width="640" height="440" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
</div>

<div>
  In my example here, the longest time it took to process a ASPX page was is the lighter pink line.  The longest time was 14 seconds.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-gf1_rU49kT8/VHK22rHF-KI/AAAAAAAAAqw/0Aay6z5gXNE/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.40.15%2BPM.png"><img src="http://3.bp.blogspot.com/-gf1_rU49kT8/VHK22rHF-KI/AAAAAAAAAqw/0Aay6z5gXNE/s1600/Screen%2BShot%2B2014-11-23%2Bat%2B9.40.15%2BPM.png" width="640" height="438" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  The Request Wait Time is how long it takes of while you wait in the queue for the ASPX page to be processed.  So the dark blue line with the spike in the bottom right was a 16 second wait time, waiting for my page to start processing, and with the request execution time being 14 seconds this particular request may have taken 30 seconds in total.
</div>

<div>
</div>

<div>
  With this information we can fairly accurately determine that we would want to keep the number of current connections to around or under 150 just to keep a safe buffer.  To do that, we could increase the number of web interface servers or break out our web server to different Application Pools.  Our Citrix web interface was originally designed with a monolithic application pool and our PNA website and other websites all run under one process.  In our situation, we could exceed our connection limit during times when Citrix Receiver checks in to enumerate applications at the same time as users trying to use the web site.  If we break the PNA website to a new application pool then we can actually double the number of connections we can process at any one time.  The limit appears to be the w3wp.exe ability to process ASPX pages.  By adding another application pool it actually creates another w3wp.exe process and if we max out the connections on one of the websites the other website/process will continue to work without issue.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->