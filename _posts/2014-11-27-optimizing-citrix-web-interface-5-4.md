---
id: 585
title: Optimizing Citrix Web Interface 5.4
date: 2014-11-27T12:12:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/11/27/optimizing-citrix-web-interface-5-4/
permalink: /2014/11/27/optimizing-citrix-web-interface-5-4/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/11/optimizing-citrix-web-interface-54.html
blogger_internal:
  - /feeds/7977773/posts/default/8430687983521035524
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - Web Interface

---
My previous post detailed [testing the limits of your Citrix Web Interface](http://trentent.blogspot.ca/2014/11/slow-citrix-web-interface-54-on-aspx.html). Â During this testing we discovered there appeared to be a limit on the number of connections the Citrix Web Interface could accommodate. Â This discovery masked the true limitation: the Citrix Web Interface is limited by the number of threads its w3wp.exe process can spawn to handle ASPX pages. Â The w3wp.exe process spawn threads to handle the number of connections/load and once it exceeded 48 threads then further requests go into the application queue. Â This limitation exists when running a ASP.NET application under ASP.NET 2.0 in **<u>Classic</u>** mode. Â In integrated mode this limitation is determined differently and not applicable. Â Unfortunately, the Citrix Web Interface runs in Classic mode under ASP.NET 2.0.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-4_SmMjyt344/VHSgXBBr9zI/AAAAAAAAArA/1lIX2R7Hm7c/s1600/queue.png"><img src="http://1.bp.blogspot.com/-4_SmMjyt344/VHSgXBBr9zI/AAAAAAAAArA/1lIX2R7Hm7c/s1600/queue.png" width="640" height="456" border="0" /></a>
</div>

This formula is determined by the number of CPU's you have times an arbitrary number Microsoft decided would be good for dealing with ASPX pages set back in the IIS 5 or IIS 6 days. Â It is woefully low and sadly, Citrix does not resolve it when you install the Web Interface application.

But, we can increase the per processor thread count to prevent the queuing. Â To do so you must edit the "C:\Windows\Microsoft.NET\Framework\v2.0.50727\CONFIG\machine.config" and set the processModel with a new set of thread values.

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-2_Ixrmnqv6Q/VHShH3tLykI/AAAAAAAAArI/ixT7Mr3c9lU/s1600/Screen%2BShot%2B2014-11-24%2Bat%2B3.13.31%2BPM.png"><img src="http://1.bp.blogspot.com/-2_Ixrmnqv6Q/VHShH3tLykI/AAAAAAAAArI/ixT7Mr3c9lU/s1600/Screen%2BShot%2B2014-11-24%2Bat%2B3.13.31%2BPM.png" width="320" height="291" border="0" /></a>
</div>

<div>
</div>

<div>
  The values are MaxWorkerThreads, MaxIoThreads, MinWorkerThreads and MinIoThreads. Â All values are set * the number of processors you have and are *per w3wp.exe process*. Â So if you have a & website and services site running on separate application pools each process then has a 1000 thread limit on a 2 processor system. Â When we implement these we found we could hammer the web interface and the ASPX pages would consistently respond without issue. Â We did find that hammering the web interface with 1000 threads then revealed a new performance limitation. Â Though the ASPX pages were being processed so the explicit login page now constantly appeared without issue, we found that logging in and showing the applications was now slow, taking several dozen seconds at times. Â By allowing our web interface to process more connections and pass those connections on; some of our dedicated XML brokers were having issues keeping up with the requests.</p> 
  
  <p>
    My next post will detail testing the capabilities of the XML server and seeing if we can determine the best way to optimize XML performance.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->