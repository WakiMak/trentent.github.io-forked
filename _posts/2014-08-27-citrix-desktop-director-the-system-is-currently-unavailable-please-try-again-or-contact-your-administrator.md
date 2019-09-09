---
id: 595
title: Citrix Desktop Director "The system is currently unavailable. Please try again or contact your administrator."
date: 2014-08-27T15:49:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/27/citrix-desktop-director-the-system-is-currently-unavailable-please-try-again-or-contact-your-administrator/
permalink: /2014/08/27/citrix-desktop-director-the-system-is-currently-unavailable-please-try-again-or-contact-your-administrator/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/citrix-desktop-director-system-is.html
blogger_internal:
  - /feeds/7977773/posts/default/3997785310779300117
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Director
---
We are attempting to add Citrix Desktop Director to our helpdesk.  We have a geographically spread organization across three cities connected by a huge ring network.  We setup one Desktop Director server and pointed it at two servers in Calgary (WSCTXZDC301 and 302).  We were able to login and all appears good.  We then setup our second Desktop Director server and pointed it to two servers in Edmonton (WSCTXZDC303 and 304).  With this server we were unable to login.  We were getting this error message:

"The system is currently unavailable. Please try again or contact your administrator."

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-dtADjHP2XT8/U_5P5C-_G9I/AAAAAAAAAhE/p0N8atyMcrA/s1600/1.png"><img src="http://1.bp.blogspot.com/-dtADjHP2XT8/U_5P5C-_G9I/AAAAAAAAAhE/p0N8atyMcrA/s1600/1.png" width="320" height="252" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Well...  That helps.
</div>

<div>
</div>

<div>
  Step 1 was to turn <a href="http://support.citrix.com/article/CTX130149">on logging for Desktop Director</a>.
</div>

<div>
</div>

<div>
  Step 2 was the reproduce the issue and analyze the log.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-tESrOISv6uA/U_5P5IbOgpI/AAAAAAAAAhA/0W5edd9GDN0/s1600/2.png"><img src="http://3.bp.blogspot.com/-tESrOISv6uA/U_5P5IbOgpI/AAAAAAAAAhA/0W5edd9GDN0/s1600/2.png" width="320" height="137" border="0" /></a>
</div>

<div>
  <pre class="lang:default decode:true ">"15:13:37.2515 : [t:5, s:unknown] TimeoutException caught: This request operation sent to net.tcp://wsctxzdc303.healthy.bewell.ca:2513/Citrix/&Commands did not receive a reply within the configured timeout (00:00:10).  The time allotted to this operation may have been a portion of a longer timeout.  This may be because the service is still processing the operation or because the service was unable to send a reply message.  Please consider increasing the operation timeout (by casting the channel/proxy to IContextChannel and setting the OperationTimeout property) and ensure that the service is able to connect to the client."</pre>
  
  <p>
    So.  It appears there is a timeout and the &Commands is not completing in time.  Is this because something was wrong with our ZDC?  To test this I <a href="http://newdelhipowershellusergroup.blogspot.ca/2012/05/citrix-and-powershell-remoting.html">used Remote-XAPSSession</a> to test the command above in the log (Get-XAServer -OnlineOnly -ZoneName A,B,C).  With <a href="http://newdelhipowershellusergroup.blogspot.ca/2012/05/citrix-and-powershell-remoting.html">powershell remoting I connected to the ZDC</a> 301 and 302 and ran the command and it completed within a second.  I then tried it against 303 and 304 and the command completed in....  17 seconds and 22 seconds.  Far exceeding the 10 second timeout.  Why was it taking so long?  It turns out that the ZDC is forced to contact the database with that command as opposed to utilize the LHC (local host cache).  Procmon shows lots of database communication and our two ZDC's 303 and 304 are in Edmonton and the database resides in Calgary; communication round-trip makes those ZDC's exceed 10 seconds.
  </p>
  
  <p>
    So...  It turns out that Desktop Director should be pointed at a data collector that sits closest to the database.  If it is too far away you may exceed this timeout and your users will be unable to login.  Unforunately, I am unable to find a way to manipulate this timeout to increase it (even though that will cause poor performance, at least users can actually operate).
  </p>
</div>
