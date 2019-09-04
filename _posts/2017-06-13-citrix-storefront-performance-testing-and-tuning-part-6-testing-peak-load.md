---
id: 2377
title: 'Citrix Storefront â€“ Performance Testing and Tuning â€“ Part 6 - Testing Peak Load'
date: 2017-06-13T15:06:52-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2377
permalink: /2017/06/13/citrix-storefront-performance-testing-and-tuning-part-6-testing-peak-load/
image: /wp-content/uploads/2017/06/LoopIcon.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - PowerShell
  - Storefront
---
In part 5Â I figured out our peak load against our existing infrastructure. Â In this part I'm going to test against a Storefront server to see if it can handle the baseline load.

This is the counters used for PNA launches:

<img class="aligncenter size-full wp-image-2427" src="http://theorypc.ca/wp-content/uploads/2017/06/Counters_with_numbers.png" alt="" width="1365" height="421" srcset="http://theorypc.ca/wp-content/uploads/2017/06/Counters_with_numbers.png 1365w, http://theorypc.ca/wp-content/uploads/2017/06/Counters_with_numbers-300x93.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/Counters_with_numbers-768x237.png 768w" sizes="(max-width: 1365px) 100vw, 1365px" /> 

The loads to test are:

<table style="height: 129px;" width="923">
  <tr>
    <td width="74">
      Peak Rates
    </td>
    
    <td width="124">
      (in per second)
    </td>
    
    <td width="76">
    </td>
    
    <td width="104">
    </td>
    
    <td width="64">
    </td>
    
    <td width="64">
    </td>
  </tr>
  
  <tr>
    <td>
      PNA Logon
    </td>
    
    <td>
      PNA App Launches
    </td>
    
    <td>
      Web Logon
    </td>
    
    <td>
      Web LaunchICA
    </td>
    
    <td>
    </td>
    
    <td>
      Connections per second
    </td>
  </tr>
  
  <tr>
    <td>
      7.531
    </td>
    
    <td>
      17.94
    </td>
    
    <td>
      1.02
    </td>
    
    <td>
      1.78
    </td>
    
    <td>
    </td>
    
    <td>
      28.271
    </td>
  </tr>
</table>

I am going to use WCAT to test PNA as WCAT is fairly easy to configure. Â From the counters we can easily target "ICA Launches Calls / second" and "Find all resources Calls / second" (and divide by ~2.5) to get to our PNA load count.

Now, I can test the PNA portion with WCAT, which is good because it can maintain a nice, steady rate. Â Web Logon's and launches though, require a PowerShell script because of some cookie manipulation.

Testing a single Web Interface server, I tested just PNA app launches (20 per second) and our Web Interface server was <span style="text-decoration: underline;">unable</span> to maintain that rate. Â The maxmium number a single Web Interface server (4vCPU 12GB RAM) was ~15 application launches per second. Â At that point my Powershell script was getting errors during its requests.

<img class="aligncenter size-full wp-image-2378" src="http://theorypc.ca/wp-content/uploads/2017/05/15_PNA_Applaunchespersecond.png" alt="" width="647" height="357" srcset="http://theorypc.ca/wp-content/uploads/2017/05/15_PNA_Applaunchespersecond.png 647w, http://theorypc.ca/wp-content/uploads/2017/05/15_PNA_Applaunchespersecond-300x166.png 300w" sizes="(max-width: 647px) 100vw, 647px" /> 

Since we load balance we could sustain ~30 PNA app launches per second with Web Interface. Â How does Storefront do? Â Can a single server maintain our peak load?

<img class="aligncenter size-full wp-image-2430" src="http://theorypc.ca/wp-content/uploads/2017/06/storefront_wcat-1.png" alt="" width="635" height="378" srcset="http://theorypc.ca/wp-content/uploads/2017/06/storefront_wcat-1.png 635w, http://theorypc.ca/wp-content/uploads/2017/06/storefront_wcat-1-300x179.png 300w" sizes="(max-width: 635px) 100vw, 635px" /> 

&nbsp;

<img class="aligncenter size-large wp-image-2432" src="http://theorypc.ca/wp-content/uploads/2017/06/FindAllResourcesPerSecond.png" alt="" width="1140" height="908" srcset="http://theorypc.ca/wp-content/uploads/2017/06/FindAllResourcesPerSecond.png 1345w, http://theorypc.ca/wp-content/uploads/2017/06/FindAllResourcesPerSecond-300x239.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/FindAllResourcesPerSecond-768x612.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />  
<img class="aligncenter size-full wp-image-2431" src="http://theorypc.ca/wp-content/uploads/2017/06/ICALaunches_per_second.png" alt="" width="1334" height="209" srcset="http://theorypc.ca/wp-content/uploads/2017/06/ICALaunches_per_second.png 1334w, http://theorypc.ca/wp-content/uploads/2017/06/ICALaunches_per_second-300x47.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/ICALaunches_per_second-768x120.png 768w" sizes="(max-width: 1334px) 100vw, 1334px" /> 

Ok, first off, I have to say I love it when the math adds up.

I was hoping for an average of 8 PNA app launches (ICA Launch Calls / second) and I got 8.6, and I was hoping for ~19 "Find All resources Calls / second" and I got 17.7. Â So I believe I'm pretty close to simulating my real peak load traffic.

<img class="aligncenter size-full wp-image-2433" src="http://theorypc.ca/wp-content/uploads/2017/06/PNA_Perf.png" alt="" width="932" height="526" srcset="http://theorypc.ca/wp-content/uploads/2017/06/PNA_Perf.png 932w, http://theorypc.ca/wp-content/uploads/2017/06/PNA_Perf-300x169.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/PNA_Perf-768x433.png 768w" sizes="(max-width: 932px) 100vw, 932px" /> 

So we can see the load testing definitely put an impact on Storefront server. Â We had some peaks, which varied between requesting icons and requesting the ICA file. Â Getting the Config.xml file didn't move the needle neither did find PRELAUNCH applications. Â I'm not entirely sure why we have some spikes but overall I think I'm pretty satisfied with this performance.

When does Storefront break (in my environment)?

I set WCAT to do 1000 connections. At 32Â connections per second my response time was starting to increase dramatically. Â This was with a 4vCPU system.

<img class="aligncenter size-full wp-image-2436" src="http://theorypc.ca/wp-content/uploads/2017/06/32conPerSecond.png" alt="" width="867" height="427" srcset="http://theorypc.ca/wp-content/uploads/2017/06/32conPerSecond.png 867w, http://theorypc.ca/wp-content/uploads/2017/06/32conPerSecond-300x148.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/32conPerSecond-768x378.png 768w" sizes="(max-width: 867px) 100vw, 867px" /> 

Monitoring my XML brokers showed they were not breaking a sweat. Â So the bottle neck has to be Storefront. Â Looking at my CPU graphs showed this to be a CPU bottle neck.

<img class="aligncenter size-full wp-image-2437" src="http://theorypc.ca/wp-content/uploads/2017/06/taskmgr.png" alt="" width="885" height="847" srcset="http://theorypc.ca/wp-content/uploads/2017/06/taskmgr.png 885w, http://theorypc.ca/wp-content/uploads/2017/06/taskmgr-300x287.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/taskmgr-768x735.png 768w" sizes="(max-width: 885px) 100vw, 885px" /> 

So what happens if I up the vCPU to 8? Â When does the bottleneck push to the brokers or no longer become the CPU?

&nbsp;

At 8vCPU StoreFront scaled nearly linearly. Â At 32 connections per second It was handling requests at around 7 seconds. Â At 38 connections per second though, it was starting to choke, going at about 10 seconds per request.

<img class="aligncenter size-full wp-image-2438" src="http://theorypc.ca/wp-content/uploads/2017/06/38con_break.png" alt="" width="833" height="294" srcset="http://theorypc.ca/wp-content/uploads/2017/06/38con_break.png 833w, http://theorypc.ca/wp-content/uploads/2017/06/38con_break-300x106.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/38con_break-768x271.png 768w" sizes="(max-width: 833px) 100vw, 833px" /> 

&nbsp;

<img class="aligncenter size-full wp-image-2440" src="http://theorypc.ca/wp-content/uploads/2017/06/38con_break_CPU.png" alt="" width="1419" height="838" srcset="http://theorypc.ca/wp-content/uploads/2017/06/38con_break_CPU.png 1419w, http://theorypc.ca/wp-content/uploads/2017/06/38con_break_CPU-300x177.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/38con_break_CPU-768x454.png 768w, http://theorypc.ca/wp-content/uploads/2017/06/38con_break_CPU-750x444.png 750w" sizes="(max-width: 1419px) 100vw, 1419px" /> 

And this is the last up I can do. Â I moved the StoreFront up to 16 vCPU with a whole host for itself to prevent CPU oversubscription.

This configuration enabled us to handle 64Â connections per second before consistently breaking the 10 second limit.

<img class="aligncenter size-large wp-image-2442" src="http://theorypc.ca/wp-content/uploads/2017/06/16vCPU_41con.png" alt="" width="881" height="721" srcset="http://theorypc.ca/wp-content/uploads/2017/06/16vCPU_41con.png 881w, http://theorypc.ca/wp-content/uploads/2017/06/16vCPU_41con-300x246.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/16vCPU_41con-768x629.png 768w" sizes="(max-width: 881px) 100vw, 881px" /> 

&nbsp;

<img class="aligncenter size-full wp-image-2443" src="http://theorypc.ca/wp-content/uploads/2017/06/64_con.png" alt="" width="941" height="208" srcset="http://theorypc.ca/wp-content/uploads/2017/06/64_con.png 941w, http://theorypc.ca/wp-content/uploads/2017/06/64_con-300x66.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/64_con-768x170.png 768w" sizes="(max-width: 941px) 100vw, 941px" /> 

&nbsp;

If we set a target of a "fail" when the response time exceeds 10 seconds this is what my maximum number of requests can be per vCPU:

<img class="aligncenter size-full wp-image-2444" src="http://theorypc.ca/wp-content/uploads/2017/06/conn_storefront_exceed10s.png" alt="" width="1322" height="697" srcset="http://theorypc.ca/wp-content/uploads/2017/06/conn_storefront_exceed10s.png 1322w, http://theorypc.ca/wp-content/uploads/2017/06/conn_storefront_exceed10s-300x158.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/conn_storefront_exceed10s-768x405.png 768w" sizes="(max-width: 1322px) 100vw, 1322px" /> 

So Storefront seems to scale pretty well with CPU, with the biggest gain going from 4vCPU to 8vCPU. Â At 4vCPU a single StoreFront server should be able to handle ourÂ entire peak load, albeit barely. Â At 8vCPU a single server should have more than enough capacity to handle almost double our peak loading.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->