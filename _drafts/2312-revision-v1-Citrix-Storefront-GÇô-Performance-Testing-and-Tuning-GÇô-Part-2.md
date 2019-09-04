---
id: 2323
title: Citrix Storefront â€“ Performance Testing and Tuning â€“ Part 2
date: 2017-05-24T16:25:31-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/05/24/2312-revision-v1/
permalink: /2017/05/24/2312-revision-v1/
---
[In part 1](https://theorypc.ca/2017/05/24/citrix-storefront-performance-testing-and-tuning-part-1/), I went through and developed a script that will simulate a user logon and launching an application through Storefront. Â This post will go through testing the limits of Storefront on our infrastructure. Â What I&#8217;m curious to know is how many simultaneous users can we have accessing Storefront before we start to experience performance problems.

I want to test the following scenarios:

  1. Citrix Storefront running on Server 2016 Core
  2. Citrix Storefront running on Server 2016 with Desktop Experience
  3. #1 and #2 with increasing numbers of vCPU&#8217;s

To make it easier to hit the limits I&#8217;m going to start with a single CPU Storefront server <span style="text-decoration: underline;">running Server 2016 Core</span>Â and then I&#8217;ll scale up to determine the impact of CPU&#8217;s.

Here is the average timing of the user logging into a 1vCPU StoreFront server and launching anÂ application. Â This is an average calculated overÂ 26 runs.

<img class="aligncenter size-full wp-image-2313" src="http://theorypc.ca/wp-content/uploads/2017/05/Storefront_1vCPU_Average.png" alt="" width="1076" height="85" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Storefront_1vCPU_Average.png 1076w, http://theorypc.ca/wp-content/uploads/2017/05/Storefront_1vCPU_Average-300x24.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Storefront_1vCPU_Average-768x61.png 768w" sizes="(max-width: 1076px) 100vw, 1076px" /> 

<img class="aligncenter size-full wp-image-2317" src="http://theorypc.ca/wp-content/uploads/2017/05/1Session_PieChart.png" alt="" width="831" height="568" srcset="http://theorypc.ca/wp-content/uploads/2017/05/1Session_PieChart.png 831w, http://theorypc.ca/wp-content/uploads/2017/05/1Session_PieChart-300x205.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/1Session_PieChart-768x525.png 768w" sizes="(max-width: 831px) 100vw, 831px" /> 

It takes 2.1 seconds from when the initial connection to our Storefront server connects, to when the ICA file is received. Â The longest portion of this time is Resource Enumeration (282ms), Downloading all the icons (945ms) and getting the launch status (667ms). Â These 3 components take up around 90% of the processing time.

So let&#8217;s look what happens when we start adding users to this Storefront server:

<img class="aligncenter size-full wp-image-2314" src="http://theorypc.ca/wp-content/uploads/2017/05/1vCPU_Sessions.png" alt="" width="1252" height="239" srcset="http://theorypc.ca/wp-content/uploads/2017/05/1vCPU_Sessions.png 1252w, http://theorypc.ca/wp-content/uploads/2017/05/1vCPU_Sessions-300x57.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/1vCPU_Sessions-768x147.png 768w" sizes="(max-width: 1252px) 100vw, 1252px" /> 

<img class="aligncenter size-full wp-image-2315" src="http://theorypc.ca/wp-content/uploads/2017/05/35sessions.png" alt="" width="819" height="241" srcset="http://theorypc.ca/wp-content/uploads/2017/05/35sessions.png 819w, http://theorypc.ca/wp-content/uploads/2017/05/35sessions-300x88.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/35sessions-768x226.png 768w" sizes="(max-width: 819px) 100vw, 819px" /> 

<img class="aligncenter size-large wp-image-2316" src="http://theorypc.ca/wp-content/uploads/2017/05/35_sessions2.png" alt="" width="909" height="700" srcset="http://theorypc.ca/wp-content/uploads/2017/05/35_sessions2.png 909w, http://theorypc.ca/wp-content/uploads/2017/05/35_sessions2-300x231.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/35_sessions2-768x591.png 768w" sizes="(max-width: 909px) 100vw, 909px" /> 

<div id="attachment_2318" style="width: 588px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2318" class="wp-image-2318 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart.png" alt="" width="578" height="583" srcset="http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart.png 578w, http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart-297x300.png 297w, http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2017/05/35Session_PieChart-100x100.png 100w" sizes="(max-width: 578px) 100vw, 578px" /></p> 
  
  <p id="caption-attachment-2318" class="wp-caption-text">
    35 Sessions
  </p>
</div>

I don&#8217;t think this looksÂ too bad. Â The biggest hit on adding concurrent Storefront sessionsÂ was pushing out the icon files. Â Getting the launch status and resource enumeration didn&#8217;t take much longer at all.

So let&#8217;s bump up the vCPU&#8217;s to 2 and see what happens:

<img class="aligncenter size-full wp-image-2319" src="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_chart.png" alt="" width="1362" height="283" srcset="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_chart.png 1362w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_chart-300x62.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_chart-768x160.png 768w" sizes="(max-width: 1362px) 100vw, 1362px" /> 

<div id="attachment_2320" style="width: 551px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2320" class="wp-image-2320 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/2VCPU_55UserPieChart.png" alt="" width="541" height="579" srcset="http://theorypc.ca/wp-content/uploads/2017/05/2VCPU_55UserPieChart.png 541w, http://theorypc.ca/wp-content/uploads/2017/05/2VCPU_55UserPieChart-280x300.png 280w" sizes="(max-width: 541px) 100vw, 541px" /></p> 
  
  <p id="caption-attachment-2320" class="wp-caption-text">
    55Â Sessions
  </p>
</div>

<img class="aligncenter size-full wp-image-2321" src="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_50_concurrentSessions.png" alt="" width="844" height="433" srcset="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_50_concurrentSessions.png 844w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_50_concurrentSessions-300x154.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_50_concurrentSessions-768x394.png 768w" sizes="(max-width: 844px) 100vw, 844px" /> 

<img class="aligncenter size-large wp-image-2322" src="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_Taskmgr.png" alt="" width="911" height="695" srcset="http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_Taskmgr.png 911w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_Taskmgr-300x229.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/2vCPU_Taskmgr-768x586.png 768w" sizes="(max-width: 911px) 100vw, 911px" /> 

Wow. Â Impressively, the amount of users vs the time it took to service their request scaled almost completely linearly.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->