---
id: 2119
title: Citrix & Enumeration Performance - IMA vs FMA load testing
date: 2017-04-12T09:17:12-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2119
permalink: /2017/04/12/citrix-&-enumeration-performance-ima-vs-fma-load-testing/
image: /wp-content/uploads/2017/04/graph.png
categories:
  - Blog
tags:
  - Citrix
  - Performance

---
In the [previous post we found IMA outperformed FMA in terms of an individual request for resources](https://theorypc.ca/2017/04/11/citrix-&-enumeration-performance-ima-vs-fma/).  However, the 30ms difference would be imperceptible for a end user.  This post will focus on maximum load for an individual broker.  In the past we discovered that the IMA service is heavily dependent on the number of CPU's.  The more [CPU's allocated to the machine hosting the IMA service](https://theorypc.ca/2014/11/27/load-testing-citrix-xml-broker/), the more requests could be handled (supposedly to a max of 16 - the maxmium number for threads for IMA).

What I have configured for my testing is a & 6.5 machine with 2 sockets and 1 vCPU's for a total of 2 cores.  I have an identical machine configured for & 7.13.

Doing my query directly to the 7.13 broker, I've discovered my request appears to be tied to the performance counter "Citrix XML Service - Transactions/sec - enumerate resources".  I can visibly see how many requests are hitting the broker through my testing, although the numbers vs what I'm sending don't necessarily line up.

For & 6.5 the performance counter that appears to tie directly to my requests is the "Citrix MetaFrame Presentation Server - Filtered Application Enumerations/sec".

Using WCAT to apply a load to & 6.5 and 7.13 and measuring the per second results I should be able to see which is more efficient over a larger load.

My WCAT options for the FMA server will be:

<pre class="lang:default decode:true">wcctl -t XML-Direct.ubr -f settings.ubr -s FMAServer -p 80 -c 10 -v 20 -w 300 -u 300 -n 300</pre>

and for IMA:

<pre class="lang:default decode:true">wcctl -t XML-Direct.ubr -f settings.ubr -s IMAServer -p 28001 -c 10 -v 20 -w 300 -u 300 -n 300</pre>

&nbsp;

My "scenario" file (XML-Direct.ubr) looks like so:

<pre class="lang:default decode:true">scenario {
	warmup	= 300;
	duration	= 300;
	cooldown	= 30;
	default {
		setheader {
			name	= "Connection";
			value	= "keep-alive";
		}

		version	= HTTP11;
		statuscode	= 0;
	}

	transaction {
		id = "PNA";
		weight = 1000;
		request {
			id = "1";
			url = "/scripts/wpnbr.dll";
			verb = POST;
			port = 28001; 
			postdata = "<?xml version=\"1.0\" encoding=\"UTF-8\"?><!DOCTYPE NFuseProtocol SYSTEM \"NFuse.dtd\"><NFuseProtocol version=\"5.4\"><RequestAppData><Scope traverse=\"subtree\"></Scope><DesiredDetails>permissions</DesiredDetails><ServerType>all</ServerType><ClientType>ica30</ClientType><ClientType>content</ClientType><Credentials><UserName>adtest90</UserName><Password encoding=\"ctx1\">PASSWORDLOL</Password><Domain type=\"NT\">HEALTHY</Domain></Credentials><ClientName>LOADTESTER</ClientName><ClientAddress addresstype=\"dot\">10.10.10.10</ClientAddress></RequestAppData></NFuseProtocol>";
			setheader {
				name = "Content-Type";
				value = "text/xml";
			}
			setheader {
				name = "Connection";
				value = "Keep-Alive";
			}
		}
	}
}
</pre>

The new FMA service has an additional counter called "Citrix XML Service - enumerate resources - Avg. Transaction Time" which actually reports back how long it took to execute the enumeration.

Just like my previous testing I'm going to test with 2, 4, 8 and now with 16 CPU.  Both machines are VM's on the same cluster with the same specs for their host (Intel Xeon E5-2680 @ 2.70GHz).  A difference between the two VM's is the XenDesktop 7.13 is a Server 2016 Standard platform.

2vCPU

<img class="aligncenter wp-image-2120 size-full" src="http://theorypc.ca/wp-content/uploads/2017/04/2vCPU.png" alt="" width="815" height="544" srcset="http://theorypc.ca/wp-content/uploads/2017/04/2vCPU.png 815w, http://theorypc.ca/wp-content/uploads/2017/04/2vCPU-300x200.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/2vCPU-768x513.png 768w" sizes="(max-width: 815px) 100vw, 815px" /> 

<p style="text-align: center;">
  <strong><span style="color: #0000ff;">IMA is in BLUE</span></strong> and <strong><span style="color: #ff6600;">FMA is in ORANGE</span></strong>.
</p>

<p style="text-align: left;">
  Interesting results.  It appears XenDesktop FMA broker can handle higher loads with much greater efficiency compared to IMA.  And it does not appear to take much for FMA to flex some muscle and show that it can handle the same load faster than IMA.  FMA kept under our 5000ms target up to 180 concurrent connections where as IMA breaks that around 140 connections.  This initial data shows that FMA could handle approximately 23% more load.  Fairly impressive.
</p>

4vCPU  
<img class="aligncenter size-full wp-image-2121" src="http://theorypc.ca/wp-content/uploads/2017/04/4vCPU.png" alt="" width="747" height="527" srcset="http://theorypc.ca/wp-content/uploads/2017/04/4vCPU.png 747w, http://theorypc.ca/wp-content/uploads/2017/04/4vCPU-300x212.png 300w" sizes="(max-width: 747px) 100vw, 747px" /> 

Again, FMA continues its dominance in enumeration performance.  The gap between them grows slightly to 26% at 400 concurrent connections.  At our target of under 5000ms, IMA breaks at 250 connections, FMA actually held until around 380 connections.

8vCPU

<img class="aligncenter size-full wp-image-2122" src="http://theorypc.ca/wp-content/uploads/2017/04/8vCPU.png" alt="" width="749" height="545" srcset="http://theorypc.ca/wp-content/uploads/2017/04/8vCPU.png 749w, http://theorypc.ca/wp-content/uploads/2017/04/8vCPU-300x218.png 300w" sizes="(max-width: 749px) 100vw, 749px" /> 

Again, FMA handles the additional load with the additional CPU's without breaking a sweat.  It seemed to be unstoppable, keeping under 5000ms until about 655 connections.  IMA, on the other hand, exceeded 5000ms response time at 400 connections.  But I had something strange happen that I assumed to be my fault.  The FMA broker service suddenly spiked at 800 concurrent connections (this graph only shows up to 780) and its response time zoomed to 30-40 <span style="text-decoration: underline;"><strong>seconds</strong></span>.  I assumed this was a fault of mine and continued on, trying 16 CPU's.

16vCPU

<img class="aligncenter size-full wp-image-2123" src="http://theorypc.ca/wp-content/uploads/2017/04/16vCPU.png" alt="" width="644" height="523" srcset="http://theorypc.ca/wp-content/uploads/2017/04/16vCPU.png 644w, http://theorypc.ca/wp-content/uploads/2017/04/16vCPU-300x244.png 300w" sizes="(max-width: 644px) 100vw, 644px" /> 

But this graph shows it pretty readily.  As soon as you cross 800 concurrent connections FMA pukes.  I didn't scale the graph to show that spike, but it goes up to 40 seconds.  So there appears to be a pretty hard limit of <800 concurrent connections (600 would probably be a pretty safe buffer...).  If you exceed that limit your performance is going to tank, HARD.  IMA, however, pushes on.  With 16vCPU IMA didn't break the 5000ms target until 570 concurrent connections.  FMA appeared to be handling it just fine until it exploded.

For this testing the FMA broker is set in 'connection leasing' mode.  Perhaps this is related to that?  My next test will be to set the broker to Local Host Cache mode, retest, and then simulate a DB failure and test the LHC and see how quickly it can respond.

Summary:

The FMA broker, when acting in a purely & fashion works pretty well.  It handles loads faster than IMA, but apparently this is only true to an extreme rate.  You should not have more than <span style="text-decoration: underline;"><strong>800</strong></span> concurrent connections per broker.  <period>.  You should probably keep a target maximum of 600 to be safe.  And assign at least 4, but preferably <span style="text-decoration: underline;"><strong>8 CPU's</strong></span> if you can to your broker servers.  This appears to be the sweet spot.

The highest rate our organization sees is 600 concurrent connections but we spread this lead across 9 IMA brokers, and divide that geographically.  The highest consecutive load we've measured with this on any single broker is ~150 concurrent connections during a peak load period.  We target a response time of less than 5 seconds for any broker enumeration and it does appear we could handle this quite easily with FMA.  However, this does not take into account XenDesktop traffic which is 'heavier' than & for the FMA.  When we get to a point that I can test XenDesktop load I will do so.  Until then, I am impressed with FMA's & enumeration performance (until breakage anyways).

[Next up...  Oddities encountered in testing](https://theorypc.ca/2017/04/13/citrix-&-enumeration-performance-ima-vs-fma-oddities/)

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->