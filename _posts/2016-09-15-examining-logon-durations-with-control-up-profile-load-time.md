---
id: 1704
title: Examining Logon Durations with Control Up - Profile Load Time
date: 2016-09-15T13:24:04-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1704
permalink: /2016/09/15/examining-logon-durations-with-control-up-profile-load-time/
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - ControlUp
  - Performance

---
Touching on my last point with an invalid AD Home Directory attribute, I decided to examine it in more detail as what is causing the slowness on logon.

<img class="aligncenter size-full wp-image-1705" src="/wp-content/uploads/2016/09/60s.png" alt="60s" width="771" height="84" srcset="/wp-content/uploads/2016/09/60s.png 771w, /wp-content/uploads/2016/09/60s-300x33.png 300w, /wp-content/uploads/2016/09/60s-768x84.png 768w" sizes="(max-width: 771px) 100vw, 771px" /> 

52 seconds for Profile Load time.  This is caused by this:

<img class="aligncenter size-full wp-image-1706" src="/wp-content/uploads/2016/09/bad_attribute.png" alt="bad_attribute" width="399" height="380" srcset="/wp-content/uploads/2016/09/bad_attribute.png 399w, /wp-content/uploads/2016/09/bad_attribute-300x286.png 300w" sizes="(max-width: 399px) 100vw, 399px" /> 

The Home Folder server 'WSAPVSEQ07' I created that share and set the home directory to it.  I then simulated a 'server migration' by shutting this box down.  This means that the server responds to pings because it's still present in DNS.

<img class="aligncenter size-full wp-image-1707" src="/wp-content/uploads/2016/09/ping.png" alt="ping" width="622" height="199" srcset="/wp-content/uploads/2016/09/ping.png 622w, /wp-content/uploads/2016/09/ping-300x96.png 300w" sizes="(max-width: 622px) 100vw, 622px" /> 

But why does it take so long?

If I do a packet capture on the server I'm trying to launch my application from, and set it to trace the ip.addr of the 'powered off' server, here's what we see:

<img class="aligncenter size-large wp-image-1708" src="/wp-content/uploads/2016/09/slow-logon-packet-capture-1024x275.png" alt="slow-logon-packet-capture" width="1024" height="275" srcset="/wp-content/uploads/2016/09/slow-logon-packet-capture-1024x275.png 1024w, /wp-content/uploads/2016/09/slow-logon-packet-capture-300x80.png 300w, /wp-content/uploads/2016/09/slow-logon-packet-capture-768x206.png 768w, /wp-content/uploads/2016/09/slow-logon-packet-capture.png 1201w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

The logon process attempts to query the server at the '28.9' second mark and stops at the '73.9' second mark.  A total of 45 seconds.  This is all lumped into the 'User Profile Load Time'.  So what's happening here?

We can see the initial attempt to connection occurs at 28.9 seconds, then 31.9 seconds, then finally 37.9 seconds.  The time span is 3s between the first and second try then 6s between the second and third try.  [This is explained here](https://support.microsoft.com/en-ca/kb/2786464).

> <div id="mt0" class="kb-symptoms-section section ng-scope">
>   In Windows 7 and Windows Server 2008 R2, the TCP maximum SYN retransmission value is set to <span class="text-base">2</span>, and is not configurable. Because of the 3-second limit of the initial time-out value, the TCP three-way handshake is limited to a 21-second timeframe (3 seconds + 2*3 seconds + 4*3 seconds = 21 seconds).
> </div>
> 
> <div id="mt1" class="kb-resolution-section section ng-scope">
>
> </div>

Is this what we are seeing?  It's close.  We are seeing the first two items for sure, but then in instead of a '3rd' attempt, it starts over but at the same formula.

<pre class="">Packet 8451 to 8508 = 3 seconds
Packet 8508 to 8600 = 6 seconds (2*3)
Packet 8600 to 8910 = 12 seconds (4*3)
Packet 8910 to 8979 = 3 seconds
Packet 8979 to 9048 = 6 seconds (2*3)
Packet 9048 to 9240 = 6 seconds (2*3?)
Packet 9240 to 9295 = 3 seconds
Packet 9295 to 9413 = 6 seconds (2*3)</pre>

= 45 seconds.

According to the KB article, we are seeing the Max SYN retransmissions (2) for each syn sent.  [This article contains a hotfix we can install](https://support.microsoft.com/en-ca/kb/2786464) to change the value of the Max SYN retransmissions, but it's a minimum of 2 which it's set to anyways.  However, there is an [additional hotfix to modify](https://support.microsoft.com/en-ca/kb/2472264) the 3 second time period.

The minimum I've found is we can reduce the 3 second time period to 100ms.

<img class="aligncenter size-full wp-image-1710" src="/wp-content/uploads/2016/09/values.png" alt="values" width="535" height="309" srcset="/wp-content/uploads/2016/09/values.png 535w, /wp-content/uploads/2016/09/values-300x173.png 300w" sizes="(max-width: 535px) 100vw, 535px" /> 

This reduces the logon time to:

<img class="aligncenter size-full wp-image-1709" src="/wp-content/uploads/2016/09/faster_logon.png" alt="faster_logon" width="770" height="84" srcset="/wp-content/uploads/2016/09/faster_logon.png 770w, /wp-content/uploads/2016/09/faster_logon-300x33.png 300w, /wp-content/uploads/2016/09/faster_logon-768x84.png 768w" sizes="(max-width: 770px) 100vw, 770px" /> 

19 seconds.

What does this packet capture look like?  Like this:  
<img class="aligncenter size-large wp-image-1711" src="/wp-content/uploads/2016/09/Lower_Syn_Values-1024x217.png" alt="lower_syn_values" width="1024" height="217" srcset="/wp-content/uploads/2016/09/Lower_Syn_Values-1024x217.png 1024w, /wp-content/uploads/2016/09/Lower_Syn_Values-300x64.png 300w, /wp-content/uploads/2016/09/Lower_Syn_Values-768x163.png 768w, /wp-content/uploads/2016/09/Lower_Syn_Values.png 1178w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

<pre class="">With 100ms Initial RTO we see the difference between the packets to be:
Packet 16246 to 18743 = 300ms
Packet 18743 to 19316 = 600ms (2*3)
Packet 19316 to 39525 = 12 seconds (4*3?)
Packet 39525 to 39537 = 300ms
Packet 39537 to 39579 = 600ms (2*3)
Packet 39579 to 39651 = 1200ms seconds (4*3?)
Packet 39651 to 39693 = 300ms
Packet 39693 to 39754 = 600ms (2*3)</pre>

Even with the 'Initial RTO' set to 100ms, Windows has a MinRTO value of 300ms:

<img class="aligncenter size-full wp-image-1712" src="/wp-content/uploads/2016/09/MinRTO.png" alt="minrto" width="479" height="153" srcset="/wp-content/uploads/2016/09/MinRTO.png 479w, /wp-content/uploads/2016/09/MinRTO-300x96.png 300w" sizes="(max-width: 479px) 100vw, 479px" /> 

After the initial 'attempt' there is a 10-12 second delay.

Setting the MinRTO to the minimum 20ms

<img class="aligncenter size-full wp-image-1713" src="/wp-content/uploads/2016/09/MinRTO_20.png" alt="minrto_20" width="614" height="424" srcset="/wp-content/uploads/2016/09/MinRTO_20.png 614w, /wp-content/uploads/2016/09/MinRTO_20-300x207.png 300w" sizes="(max-width: 614px) 100vw, 614px" /> 

Reduces our logon time further now because our SYN packets are now about 200ms apart:

<img class="aligncenter size-large wp-image-1714" src="/wp-content/uploads/2016/09/MinRTO_200ms-1024x243.png" alt="minrto_200ms" width="1024" height="243" srcset="/wp-content/uploads/2016/09/MinRTO_200ms-1024x243.png 1024w, /wp-content/uploads/2016/09/MinRTO_200ms-300x71.png 300w, /wp-content/uploads/2016/09/MinRTO_200ms-768x182.png 768w, /wp-content/uploads/2016/09/MinRTO_200ms.png 1169w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

We are now 16 seconds, 13 seconds spent on the profile upon which 12 seconds was timing out this connection.

<img class="aligncenter size-full wp-image-1715" src="/wp-content/uploads/2016/09/16s-logon.png" alt="16s-logon" width="773" height="86" srcset="/wp-content/uploads/2016/09/16s-logon.png 773w, /wp-content/uploads/2016/09/16s-logon-300x33.png 300w, /wp-content/uploads/2016/09/16s-logon-768x85.png 768w" sizes="(max-width: 773px) 100vw, 773px" /> 

&nbsp;

Would you implement this?  I would strongly recommend against it.  SYN's were designed so blips in the network can be overcome.  Unfortunately, I know of no way to get around the 'Home Directory responds to DNS but not to ping' timeout.  The following group policies have no effect:

<img class="aligncenter size-full wp-image-1716" src="/wp-content/uploads/2016/09/GPO1.png" alt="gpo1" width="696" height="636" srcset="/wp-content/uploads/2016/09/GPO1.png 696w, /wp-content/uploads/2016/09/GPO1-300x274.png 300w" sizes="(max-width: 696px) 100vw, 696px" /> 

<img class="aligncenter size-large wp-image-1717" src="/wp-content/uploads/2016/09/GPO2.png" alt="gpo2" width="701" height="637" srcset="/wp-content/uploads/2016/09/GPO2.png 701w, /wp-content/uploads/2016/09/GPO2-300x273.png 300w" sizes="(max-width: 701px) 100vw, 701px" /> 

&nbsp;

And I suspect the reason it has no effect is because the server still responds to DNS so the SYN sequence takes place and blocks regardless of this GPO settings.  Undoubtedly, it's because this comes into play:  
<img class="aligncenter size-full wp-image-1719" src="/wp-content/uploads/2016/09/GPO_Explain.png" alt="gpo_explain" width="644" height="280" srcset="/wp-content/uploads/2016/09/GPO_Explain.png 644w, /wp-content/uploads/2016/09/GPO_Explain-300x130.png 300w" sizes="(max-width: 644px) 100vw, 644px" /> 

Since our user has a home directory the preference is to 'always wait for the network to be initialized before logging the user on'.  There is no override.

Is there a way to detect a dead home directory server during a logon?  Outside of long logon's I don't see any event logging or anything that would point us to determine if this is the issue without having to do a packet capture in a isolated environment.

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->