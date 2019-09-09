---
id: 2744
title: Meltdown + Spectre - Performance Analysis
date: 2018-04-30T12:15:52-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2744
permalink: /2018/04/30/meltdown-spectre-performance-analysis/
image: /wp-content/uploads/2018/04/Spectre.png
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - Meltdown
  - Performance
  - Registry
  - Spectre

---
Meltdown and Spectre (variant 2) are two vulnerabilities that came out at the same time, however they are vastly different.  Patches for both were released extremely quickly for Microsoft OS's but because of a variety of issues with Spectre, only Meltdown was truly available to be mitigated.  Spectre (variant 2) mitigation had a problematic release, causing numerous issues for whomever installed the fix, that it had to be recalled and the release delayed weeks.  However, around March/April 2018, the release of the Spectre patch was finalized and the microcode released.

# Threat on Performance

Spectre (variant 2), of the two, threatened to degrade performance fairly drastically.  Initial benchmarks made mention that storage was hit particularly hard.  Microsoft made comments that server OS's could be hit particularly hard.  Even worse, older operating systems would not support CPU features (PCID) that could reduce the performance impact.  Older OS's suffer more due to the design (at the time) that involved running more code in kernel mode (fonts was signalled out as an example of one of these design decision) than newer OS's.

As with most things on my blog I am particularly interested in the impact against Citrix/Remote Desktop Services type of workloads.  I wanted to test ON/OFF workloads of the mitigation impacts.

# Setup

My setup consists of two ESXi (version 6.0) hosts with identical VM's on each, hosting identical applications.  I was able to setup 4 of these pairs of hosts.  Each pair of hosts have identical processors.  The one big notable change is one host of each pair has the Spectre and Meltdown patch applied to the ESXi hypervisor.

The operating system of all the VM's is Windows Server 2008 R2.  Applications are published from Citrix & 6.5.

&nbsp;

<img class="aligncenter size-large wp-image-2745" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-21-at-9.33.27-PM-1600x290.png" alt="" width="1140" height="207" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-21-at-9.33.27-PM-1600x290.png 1600w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-21-at-9.33.27-PM-300x54.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-21-at-9.33.27-PM-768x139.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

This is simply a snapshot of a single point in time to show the metrics of these systems.

# Performance Considerations

Performance within a Citrix & farm can be described in two ways.  Capacity and speed.

### Speed

Generally, one would test for a "best case" test of the speed aspect of your applications performance.

A simplified view of this is "how fast can the app do X task?"

This task can be anything.  I've seen it measured by an automated script flipping through tabs of an application, where each tab pulled data from a database - rendered it - then moved on to the next tab.  The total time to execute these tasks amounted to a number that they used to baseline the performance of this application.

I've seen it measured as simply opening an excel document with macros and lots of formulas that pull data and perform calculations and measuring that duration.

The point of each exercise is to generate a baseline that both the app team and the Citrix team can agree to.  I've almost never had the baseline equal "real world" workloads, typically the test is an exaggeration of the actual workflow of users (eg, the test exaggerates CPU utilization).  Sometimes this is communicated and understood, other times not, but hopefully it gives you a starting point.

In general, and for Citrix workloads specifically, running the baseline test on a desktop usually produces a reasonable number of, "well... if we don't put it on Citrix this is what performance will be like so this is our minimum expectation."  Sometimes this is helpful.

Once you've established the _speed_ baseline you can now look at capacity.

### Capacity

After establishing some measurable level of performance within the application(s) you should now be able to test capacity.

If possible, start loading up users or test users running the benchmark.  Eventually, you'll hit a point where the server fails - either because it ran out of resources, performance degraded so much it errors, etc.  If you do it right, you should be able to start to find the curve that intersects descending performance with your "capacity".

At this point, cost may come into consideration.

Can you afford ANY performance degradation?

If not, than the curve is fairly easy.  At user X we start to see performance degrade so X-1 is our capacity.

If yes, at what point does performance degrade so much that adding users stops making sense?  Using the "without Citrix this is how it performs on the desktop" can be helpful to establish a minimum level of performance that the Citrix solution cannot cross.

Lastly, if you have network bound applications, and you have an appropriately designed Citrix solution where the app servers sit immediately beside the network resources on super-high bandwidth, ultra-low latency you may never experience performance degradation (lucky you!).  However, you may hit resource constraints in these scenarios.  Eg, although performance of the application is dependent on network, the application itself uses 1GB of RAM per instance of the application - you'll be limited pretty quickly be the amount of RAM you can have in your VM's.  These cases are generally preferred because the easy answer to increase capacity is \*more hardware\* but sometimes you can squeeze some more users with software like AppSense or WEM.

# Spectre on Performance

So what is the impact Spectre has on performance - speed and/or capacity?

If Spectre simply makes a task take longer, but you can fit the same number of tasks on a given VM/Host/etc. then the impact is only on speed. Example: a task that took 5 seconds at 5% CPU utilization now takes 10 seconds at 5% CPU utilization.  Ideally, the capacity should be identical even though the task now takes twice as long.

If Spectre makes things use \*more\* resources, but the speed is the same, then the impact is only on capacity.  Example: a task that took 5 seconds at 5% CPU utilization now takes 10% CPU utilization.  In this scenario, the performance should be identical but your capacity is now halved.

The worst case scenario is if the impact is on both, speed and capacity.  In this case, neither are recoverable except you might be able to make up some speed with newer/faster hardware.

I've tested to see the impacts of Spectre in my world.  This world consists of Windows 2008 R2 with & 6.5 on hardware that is 6 years old.  I was also able to procure some newer hardware to measure the impact there as well.

# Test Setup

Testing was accomplished by taking 2 identically configured ESXi hosts, applying the VMWare ESXi patch with the microcode for Spectre mitigation to one of the hosts, and enabling it in the operating system.  I added identical Citrix VM's to both hosts and enabled user logins to start generating load.

&nbsp;

Performance needs to measured at two levels.  At the Windows/VM level, and at the hypervisor/host level.  This is because the Hypervisor may pickup the additional work required for the mitigation that the operating system may not, and also due to the way Windows 2008 R2 does not accurately measure CPU performance.

### Windows/VM Level - Speed

I used ControlUp to measure and capture performance information.  ControlUp is able to capture various metrics including average logon duration.  This singular metric includes various system interactions, from using the network by querying Active Directory, pulling files from network shares, disk to store group policies in a cache, CPU processing which policies are applicable, and executables being launched in a sequence.  I believe that measuring logons is a good proxy for understanding the performance impact.  So lets see some numbers:

<img class="aligncenter size-full wp-image-2748" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.04.19-AM.png" alt="" width="808" height="286" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.04.19-AM.png 808w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.04.19-AM-300x106.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.04.19-AM-768x272.png 768w" sizes="(max-width: 808px) 100vw, 808px" /> 

&nbsp;

The top 3 results are Spectre enabled machines, the bottom 3 are without the patch.  The results are not good.  We are seeing a 200% **speed** impact in this metric.

With ControlUp we can drill down further into the impact:

<div id="attachment_2749" style="width: 519px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2749" class="wp-image-2749 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.19-AM.png" alt="" width="509" height="84" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.19-AM.png 509w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.19-AM-300x50.png 300w" sizes="(max-width: 509px) 100vw, 509px" /></p> 
  
  <p id="caption-attachment-2749" class="wp-caption-text">
    Without Spectre Patch
  </p>
</div>

&nbsp;

<div id="attachment_2750" style="width: 523px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2750" class="wp-image-2750 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.12-AM.png" alt="" width="513" height="95" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.12-AM.png 513w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.24.12-AM-300x56.png 300w" sizes="(max-width: 513px) 100vw, 513px" /></p> 
  
  <p id="caption-attachment-2750" class="wp-caption-text">
    With Spectre Patch
  </p>
</div>

&nbsp;

The component that took the largest hit is Group Policy.  Again, ControlUp can drill down into this component.

<div id="attachment_2754" style="width: 273px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2754" class="wp-image-2754 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.33.00-AM.png" alt="" width="263" height="177" /></p> 
  
  <p id="caption-attachment-2754" class="wp-caption-text">
    Without Spectre
  </p>
</div>

&nbsp;

<div id="attachment_2753" style="width: 276px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2753" class="wp-image-2753 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.33.07-AM.png" alt="" width="266" height="171" /></p> 
  
  <p id="caption-attachment-2753" class="wp-caption-text">
    With Spectre
  </p>
</div>

All group policy preference components take a 200% hit.  The Group Policy Preferences functions operate by pulling down an XML file from the SYSVOL store, reading the XML file, than applying whatever resultant set of policies finds applicable.  In order to trace down further to find more differences, I logged into each type of machine, one with Spectre and one without, and started a Process Monitor trace.  Group Policy is applied via the Group Policy service, which a seperate instance of the svchost.exe.  The process can be found via Task Manager:

<img class="aligncenter size-full wp-image-2755" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.44.02-AM.png" alt="" width="446" height="131" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.44.02-AM.png 446w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.44.02-AM-300x88.png 300w" sizes="(max-width: 446px) 100vw, 446px" /> 

Setting ProcMon to filter only on that PID we can begin to evaluate the performance.  I relogged in with procmon capturing the logon.

<div id="attachment_2756" style="width: 1150px" class="wp-caption aligncenter">
  <a href="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.53.10-AM.png"><img aria-describedby="caption-attachment-2756" class="wp-image-2756 size-large" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.53.10-AM-1600x1239.png" alt="" width="1140" height="883" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.53.10-AM-1600x1239.png 1600w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.53.10-AM-300x232.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-11.53.10-AM-768x595.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /></a></p> 
  
  <p id="caption-attachment-2756" class="wp-caption-text">
    Spectre Patched system on left, no patch on right
  </p>
</div>

Using ProcessMonitor, we can look at the various "Summaries" to see which particular component may be most affected:

<img class="aligncenter size-full wp-image-2757" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.35.45-PM.png" alt="" width="1027" height="622" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.35.45-PM.png 1027w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.35.45-PM-300x182.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.35.45-PM-768x465.png 768w" sizes="(max-width: 1027px) 100vw, 1027px" /> 

<img class="aligncenter size-large wp-image-2758" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.33.08-PM.png" alt="" width="559" height="351" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.33.08-PM.png 559w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.33.08-PM-300x188.png 300w" sizes="(max-width: 559px) 100vw, 559px" />  
<img class="aligncenter size-large wp-image-2759" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.24.58-PM.png" alt="" width="874" height="494" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.24.58-PM.png 874w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.24.58-PM-300x170.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.24.58-PM-768x434.png 768w" sizes="(max-width: 874px) 100vw, 874px" /> 

We see that 8.45 seconds is spent on the registry, 0.40 seconds on file actions, 1.04 seconds on the ProcessGroupPolicyExRegistry instruction.

The big ticket item is the time spent with the registry.

So how does it compare to a non-spectre system?

<img class="aligncenter size-full wp-image-2761" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.49.17-PM.png" alt="" width="942" height="367" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.49.17-PM.png 942w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.49.17-PM-300x117.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.49.17-PM-768x299.png 768w" sizes="(max-width: 942px) 100vw, 942px" /> 

<img class="aligncenter size-large wp-image-2762" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.27-PM.png" alt="" width="616" height="328" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.27-PM.png 616w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.27-PM-300x160.png 300w" sizes="(max-width: 616px) 100vw, 616px" /> 

<img class="aligncenter size-large wp-image-2763" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.09-PM.png" alt="" width="705" height="322" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.09-PM.png 705w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.47.09-PM-300x137.png 300w" sizes="(max-width: 705px) 100vw, 705px" /> 

&nbsp;

We see that 1.97 seconds is spent on the registry, 0.33 seconds on file actions, 0.24 seconds on the ProcessGroupPolicyExRegistry instruction.

Here's a table showing the results:

<img class="aligncenter size-full wp-image-2766" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.09.07-PM.png" alt="" width="281" height="68" /> 

So it definitely appears we need to look at the registry actions.  One of the cool things about Procmon is you can set a filter on your trace and open up the summaries and it will show you only the objects in the filter.  I set a filter for RegSetValue to see what the impact is for setting values in the registry:

<div id="attachment_2764" style="width: 646px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2764" class="wp-image-2764 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.55.04-PM.png" alt="" width="636" height="277" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.55.04-PM.png 636w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.55.04-PM-300x131.png 300w" sizes="(max-width: 636px) 100vw, 636px" /></p> 
  
  <p id="caption-attachment-2764" class="wp-caption-text">
    RegSetValue - without spectre applied
  </p>
</div>

&nbsp;

<div id="attachment_2765" style="width: 640px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2765" class="wp-image-2765 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.54.53-PM.png" alt="" width="630" height="261" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.54.53-PM.png 630w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-12.54.53-PM-300x124.png 300w" sizes="(max-width: 630px) 100vw, 630px" /></p> 
  
  <p id="caption-attachment-2765" class="wp-caption-text">
    RegSetValue - with spectre applied
  </p>
</div>

1,079 RegSetValue events and a 4x performance degradation.  Just to test if it is specific to write events I changed the procmon filter to filter on "category" "Read"

&nbsp;

<div id="attachment_2767" style="width: 628px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2767" class="wp-image-2767 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.24-PM.png" alt="" width="618" height="299" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.24-PM.png 618w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.24-PM-300x145.png 300w" sizes="(max-width: 618px) 100vw, 618px" /></p> 
  
  <p id="caption-attachment-2767" class="wp-caption-text">
    Registry Reads - Spectre applied
  </p>
</div>

&nbsp;

<div id="attachment_2768" style="width: 544px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2768" class="wp-image-2768 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.18-PM.png" alt="" width="534" height="278" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.18-PM.png 534w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.13.18-PM-300x156.png 300w" sizes="(max-width: 534px) 100vw, 534px" /></p> 
  
  <p id="caption-attachment-2768" class="wp-caption-text">
    Registry Reads - Spectre not applied
  </p>
</div>

We see roughly the same ratio of performance degradation, perhaps a little more so.  As a further test I created a PowerShell script that will just measure creating 1000 registry values and test it on each system:

<div id="attachment_2769" style="width: 856px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2769" class="wp-image-2769 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.21.33-PM.png" alt="" width="846" height="495" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.21.33-PM.png 846w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.21.33-PM-300x176.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.21.33-PM-768x449.png 768w" sizes="(max-width: 846px) 100vw, 846px" /></p> 
  
  <p id="caption-attachment-2769" class="wp-caption-text">
    Spectre Applied
  </p>
</div>

&nbsp;

<div id="attachment_2770" style="width: 877px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2770" class="wp-image-2770 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.20.27-PM.png" alt="" width="867" height="446" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.20.27-PM.png 867w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.20.27-PM-300x154.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.20.27-PM-768x395.png 768w" sizes="(max-width: 867px) 100vw, 867px" /></p> 
  
  <p id="caption-attachment-2770" class="wp-caption-text">
    Spectre Not Applied
  </p>
</div>

&nbsp;

A 2.22x reduction in performance.  But this is writing to the HKCU...  which is a much smaller file.  What happens if I force a change on the much larger HKLM?

<div id="attachment_2771" style="width: 807px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2771" class="wp-image-2771 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.46-PM.png" alt="" width="797" height="305" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.46-PM.png 797w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.46-PM-300x115.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.46-PM-768x294.png 768w" sizes="(max-width: 797px) 100vw, 797px" /></p> 
  
  <p id="caption-attachment-2771" class="wp-caption-text">
    Spectre Applied
  </p>
</div>

&nbsp;

<div id="attachment_2772" style="width: 845px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2772" class="wp-image-2772 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.36-PM.png" alt="" width="835" height="335" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.36-PM.png 835w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.36-PM-300x120.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-1.24.36-PM-768x308.png 768w" sizes="(max-width: 835px) 100vw, 835px" /></p> 
  
  <p id="caption-attachment-2772" class="wp-caption-text">
    Spectre Not Applied
  </p>
</div>

&nbsp;

Wow.  The size of the registry hive makes a big difference in performance.  We go from 2.22x to 3.42x performance degradation.  So on a minute level, Spectre appears to have a large impact on Registry operations and the larger the hive the worse the impact.  With this information there is a large element of sense as to why Spectre may impact Citrix/RDS more.  Registry operations occur with a high frequency in this world, and logon's highlight it even more as group policy and the registry are very intertwined.

This actually brings to mind another metric I can measure.  We have a very large AppV package that has a 80MB registry hive that is applied to the SOFTWARE hive when the package is loaded.  The difference in the amount of time (in seconds) loading this package is:

"583.7499291" (not spectre system)  
"2398.4593479" (spectre system)

This goes from 9.7 mins to 39.9 minutes.  Another 4x drop in performance and this would be predominately registry related.  So another bullet that registry operations are hit very hard.

### Windows/VM Level - Capacity

Does Spectre affect the capacity of our Citrix servers?

I recorded the CPU utilization of several VM's that mirror each other on hosts that mirror each other with a singular difference.  One set had the Spectre mitigation enabled.  I then took their CPU utilization results:

<div id="attachment_2774" style="width: 1150px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2774" class="wp-image-2774 size-large" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.47.04-PM-1600x941.png" alt="" width="1140" height="670" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.47.04-PM-1600x941.png 1600w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.47.04-PM-300x176.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.47.04-PM-768x452.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /></p> 
  
  <p id="caption-attachment-2774" class="wp-caption-text">
    Red = VM with Spectre, Blue = VM without Spectre
  </p>
</div>

By just glancing at the data we can see that the Spectre VM's had higher peaks and they appear higher more consistently.  Since "spiky" data is difficult to read, I smoothed out the data using a moving average:

<div id="attachment_2776" style="width: 1150px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2776" class="wp-image-2776 size-large" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.51.04-PM-1600x601.png" alt="" width="1140" height="428" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.51.04-PM-1600x601.png 1600w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.51.04-PM-300x113.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-6.51.04-PM-768x288.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /></p> 
  
  <p id="caption-attachment-2776" class="wp-caption-text">
    Red = VM with Spectre, Blue = VM without Spectre
  </p>
</div>

We can get a better feel for the separation in CPU utilization between Spectre enabled/disabled.  We are seeing clearly higher utilization.

Lastly, I took all of the results for each hour and produced a graph in an additive model:

<img class="aligncenter size-large wp-image-2777" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-7.03.54-PM-1600x832.png" alt="" width="1140" height="593" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-7.03.54-PM-1600x832.png 1600w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-7.03.54-PM-300x156.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-26-at-7.03.54-PM-768x399.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

This graph gives a feel for the impact during peak hours, and helps smooth out the data a bit further.  I believe what I'm seeing with each of these graphs is a performance hit measured by the VM at 25%-35%.

### Host Level - Capacity

Measuring from the host level can give us a much more accurate picture of actual resources consumed.  Windows 2008 R2 isn't a very accurate counter and so if there are lots of little slices, they can add up.

My apologies for swapping colors.  Raw data:

<div id="attachment_2779" style="width: 1234px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2779" class="wp-image-2779 size-full" src="http://theorypc.ca/wp-content/uploads/2018/04/rawdata.png" alt="" width="1224" height="866" srcset="http://theorypc.ca/wp-content/uploads/2018/04/rawdata.png 1224w, http://theorypc.ca/wp-content/uploads/2018/04/rawdata-300x212.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/rawdata-768x543.png 768w" sizes="(max-width: 1224px) 100vw, 1224px" /></p> 
  
  <p id="caption-attachment-2779" class="wp-caption-text">
    Blue = Spectre Applied, Red = No Spectre
  </p>
</div>

Very clearly we can see the hosts with Spectre applied consume more CPU resources, even coming close to consuming 100% of the CPU resources on the hosts.  Smoothing out the data using moving averages reveals the gap in performance with more clarity.

<img class="aligncenter size-full wp-image-2780" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.25.48-AM.png" alt="" width="1128" height="1009" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.25.48-AM.png 1128w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.25.48-AM-300x268.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.25.48-AM-768x687.png 768w" sizes="(max-width: 1128px) 100vw, 1128px" /> 

&nbsp;

Showing an hourly "Max CPU" per hour hit gives another visualization of the performance hit.

<img class="aligncenter size-full wp-image-2781" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.27.13-AM.png" alt="" width="1236" height="1005" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.27.13-AM.png 1236w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.27.13-AM-300x244.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-27-at-12.27.13-AM-768x624.png 768w" sizes="(max-width: 1236px) 100vw, 1236px" /> 

&nbsp;

# Summary

Windows 2008 R2, for Citrix/RDS workloads will be impacted quite highly.  The impact that I've been able to measure appears to be focused on registry-related activities.  Applications that store their settings/values/preferences in registry hives, whether they be the SOFTWARE/SYSTEM/HKCU hive will feel a performance impact.  Logon actions on RDS servers would be particularly impacted because group policies are largely registry related items, thus logon times will increase as it takes longer to process reads and writes.  CPU utilization is higher on both the Windows VM-level and the hypervisor level,  <span style="text-decoration: underline;"><strong>up to 40%.</strong></span>  The impact of speed on the applications and other functions is notable although more difficult to measure.  I was able to measure a ~400% degradation in performance for CPU processing for Group Policy Preferences, but perception is a real thing, so going from 100ms to 400ms may not be noticed. However, on applications that measure response time, it was found we had a performance impact of <span style="text-decoration: underline;"><strong>165%</strong></span>. What took 1000ms now takes 1650ms.

At the time of this writing, I was only able to quantify the _**performance**_ impact between two of the different hosts. The Intel Xeon E5-2660 v4 and Intel Xeon E5-2680.

The Intel Xeon E5-2660 v4 has a frequency of <u>26% less</u> than the older 2680. In order to overcome this handicap, the processor must have improved at a per-clock rate higher than the 26% frequency loss. [CPUBenchMark](https://www.cpubenchmark.net/) had the two processors with a single thread CPU score of _<u>1616</u>_ <u>for the Intel Xeon E5-2660 v4</u> and _<u>1657</u>_ <u>for the Intel Xeon E5-2680</u>. This put them close but after 4 years the 2680 was marginally faster. This has played out in our testing that the higher frequency processor is faster. Performance degradation for the two processors came out as such:

&nbsp;

<table width="0">
  <tr>
    <td width="264">
      CPU
    </td>
    
    <td width="186">
      Performance Hit
    </td>
  </tr>
  
  <tr>
    <td width="264">
      Intel Xeon E5-2680 2.70GHz
    </td>
    
    <td width="186">
      155%
    </td>
  </tr>
  
  <tr>
    <td width="264">
      Intel Xeon E5-2660 v4 2.00GHz
    </td>
    
    <td width="186">
      170%
    </td>
  </tr>
</table>

&nbsp;

This tells that **_frequency of the processor is more important to mitigate the performance hit._**

Keep in mind, these findings are my own. It's what I've experienced in my environment with the products and operating systems we use. Newer operating systems are supposed to perform better, but I don't have the ability to test that currently so I'm sharing these numbers as this is an absolute worst case type of scenario that you might come across. Ensure you test the impact to understand how your environment will be affected!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
