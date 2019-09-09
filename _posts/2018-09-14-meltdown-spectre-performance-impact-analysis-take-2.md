---
id: 2799
title: Meltdown + Spectre - Performance Impact Analysis (Take 2)
date: 2018-09-14T11:32:12-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2799
permalink: /2018/09/14/meltdown-spectre-performance-impact-analysis-take-2/
image: /wp-content/uploads/2018/09/Graph.png
categories:
  - Blog
tags:
  - 2008R2
  - 2012R2
  - "2016"
  - ControlUp
  - LoginVSI
  - Performance
---
I want to start this off by thanking **ControlUp**, **LoginVSI** and **Ryan Ververs-Bijkerk** for his assistance in helping me with this post.

Building on my last evaluation of the performance impact of Meltdown and Spectre, I was graciously given a trial of the LoginVSI software product used to simulate user loads and ControlUp's cloud based analytic tool, ControlUp Insights.

This analysis takes into consideration the differences of operating systems and uses the latest Dell server hardware platform with a Intel Gold 6150 as the processor at its heart. &nbsp;This processor contains the full PCID instructions to give the best performance (as of today) with mitigations enabled. &nbsp;However, only Server 2012R2 and Server 2016 can take advantage of these hardware features.

### Test Setup

This test was setup on 4 hosts. &nbsp;2 of the hosts had VM's where all the mitigations were enabled and 2 hosts had all of the mitigation features disabled. &nbsp;I tested live production workloads and simulated user loads from LoginVSI. &nbsp;The live production workloads were run on & 6.5 on 2008R2 and the simulated workloads were on & 7.15CU2 with 2008R2, 2012R2 and 2016.

Odd host numbers had the mitigation disabled, even host number had the mitigation enabled.

I sorted my testing logically in ControlUp by folder.

<img width="166" height="149" class="aligncenter size-full wp-image-2800" alt="" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-8.57.08-PM.png" /> 



# Real World Production results

The ControlUp Insights cloud product produced graphs and results that were easy and quick to interpret.&nbsp; These results are for & 6.5, Server 2008R2.

### Hosts View

#### CPU Utilization  
<img width="1512" height="472" class="aligncenter size-full wp-image-2801" alt="" src="http://theorypc.ca/wp-content/uploads/2018/06/CPUUtilization.png" srcset="http://theorypc.ca/wp-content/uploads/2018/06/CPUUtilization.png 1512w, http://theorypc.ca/wp-content/uploads/2018/06/CPUUtilization-300x94.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/CPUUtilization-768x240.png 768w" sizes="(max-width: 1512px) 100vw, 1512px" /> 

The <span style="color: #0000ff;">mitigation-<span style="text-decoration: underline;">disabled</span> "Host 1"</span> had a higher consistency of using less CPU than the <span style="color: #ff0000;">mitigation-<span style="text-decoration: underline;">enabled</span> "Host 2".&nbsp;<span style="color: #000000;">The biggest spread in CPU was ~20% on the Intel Gold 6150's with mitigation enabled to disabled.</span></span>

#### IO Utilization

<img width="1512" height="472" class="aligncenter size-full wp-image-2802" alt="" src="http://theorypc.ca/wp-content/uploads/2018/06/IOUtilization.png" srcset="http://theorypc.ca/wp-content/uploads/2018/06/IOUtilization.png 1512w, http://theorypc.ca/wp-content/uploads/2018/06/IOUtilization-300x94.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/IOUtilization-768x240.png 768w" sizes="(max-width: 1512px) 100vw, 1512px" /> 

Another interesting result was IO utilization increased by an average of 100 IOPS for mitigation-enabled VM's. &nbsp;This means that Meltdown/Spectre also tax the storage subsystem more. &nbsp;This averaged out to a consistent 12% hit in performance.



### User Experience

The logon duration of the VM's increased 43% from an average of 8 seconds on a mitigation-disabled VM to 14 seconds on a mitigation enabled VM. &nbsp;The biggest jump in the sub-metrics were Logon Duration (Other) and Group Policy time going from 3-5s and 6-8s.

<img width="1004" height="1462" class="aligncenter size-full wp-image-2803" alt="" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.43.14-PM.png" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.43.14-PM.png 1004w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.43.14-PM-206x300.png 206w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.43.14-PM-768x1118.png 768w" sizes="(max-width: 1004px) 100vw, 1004px" /> 

<p style="text-align: center;">
  <span style="text-decoration: underline;"><span style="color: #0000ff; text-decoration: underline;">Mitigation Disabled</span></span>
</p>

<img width="995" height="1463" class="aligncenter size-large wp-image-2804" alt="" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.45.00-PM.png" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.45.00-PM.png 995w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.45.00-PM-204x300.png 204w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-13-at-9.45.00-PM-768x1129.png 768w" sizes="(max-width: 995px) 100vw, 995px" /> 

<p style="text-align: center;">
  <span style="text-decoration: underline; color: #ff0000;">Mitigation Enabled</span>
</p>



For applications we have that measure "user interactivity" the reduction in the user experience was 18%. &nbsp;This meant that an action on a mitigation-enabled VM took an average of **1180ms** vs **990ms** on a mitigation-disabled VM when measuring actions within the UI.

Honestly, I wish I had ControlUp Insights earlier when I did my original piece, it provides much more detail in terms of tracking additional metrics and presents it much more cleanly that I did.&nbsp; Also, when the information was available it was super quick and fast to look and compare the various types of results.

# Simulated Results

LoginVSI was gracious enough to grant me licenses to their software for this testing.&nbsp; Their software provides simulated user actions including pauses for coffee and chatting between work like typing/sending emails, reading word or PDF documents, or reviewing presentations.&nbsp; The suite of software tested by the users tends to be major applications produced by major corporations who have experience producing software.&nbsp; It is not representative of applications that could be impacted the most by Spectre/Meltdown (generally, applications that are registry event heavy).&nbsp; Regardless, it is interesting to test with these simulated users as the workload produced by them do fall under the spectrum of "real world".&nbsp; As with everything, your mileage will vary and it is important to test and record your before and after impacts.&nbsp; ControlUp with Insights does an incredible job of this and you can easily compare different periods in time to measure the impacts, or just rely on the machine learning suggestions of their virtual experts to properly size your environment.

Since our production workloads are Windows Server 2008R2 based, I took advantage of the LoginVSI license to test all three available server operating systems: 2008R2, 2012R2, and 2016.&nbsp; Since newer operating systems are supposed to enable performance enhancing hardware features that can reduce the impact of these vulnerabilities, I was curious to \*how much\*.&nbsp; I have this information now.&nbsp;

I tested user loads of 500, 300, and 100 users across 2 hosts.&nbsp; I tested one with Spectre/Meltdown applied and one without.&nbsp; Each host ran 15VM's with each VM having 30GB RAM and 6vCPU for an CPU oversubscription of 2.5:1.&nbsp; The host spec was a Dell PowerEdge M640 with Intel 6150 Gold processors and 512GB of memory.

### 2016 - Hosts View<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-500_2.png" alt="" class="wp-image-2841" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-500_2.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-500_2-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-500_2-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

500 LoginVSI users with this workload, on Server 2016 pegged the hosts CPU to 100% on both the Meltdown/Spectre enabled and disabled hosts. We can still see the gap between the CPU utilization between the two with the Meltdown Spectre hosts<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-300_2-1.png" alt="" class="wp-image-2843" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-300_2-1.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-300_2-1-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-300_2-1-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

300 LoginVSI users with this workload, on Server 2016 we see the gap is narrow but still visible.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-100-2.png" alt="" class="wp-image-2847" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-100-2.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-100-2-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2016-100-2-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

100 LoginVSI users with this workload, on Server 2016 we see the gap is barely visible, it looks even.

### 2012R2 - Hosts View<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-500-1.png" alt="" class="wp-image-2849" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-500-1.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-500-1-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-500-1-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

500 LoginVSI users in this workload on Server 2012 R2. There definitely appears to be a much larger gap between the meltdown enabled and disabled hosts. And Server 2012R2 non-mitigated doesn't cap out like Server 2016.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-300.png" alt="" class="wp-image-2850" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-300.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-300-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-300-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

300 LoginVSI users in this workload on Server 2012R2. The separation between enabled and disabled is still very prominent.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-100.png" alt="" class="wp-image-2851" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-100.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-100-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2012-100-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

100 LoginVSI users in this workload on Server 2012R2. Again, the separation is noticeable but appears narrower with lighter loads.

### 2008R2 - Hosts View<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-500.png" alt="" class="wp-image-2852" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-500.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-500-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-500-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

500 LoginVSI users in this workload on Server 2008R2.  Noticeable additional CPU load with the Meltdown/Spectre host. A more interesting thing is it apperas overall CPU utilization is lower than 2012R2 or 2016.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-300.png" alt="" class="wp-image-2853" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-300.png 1213w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-300-300x124.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-2008-300-768x318.png 768w" sizes="(max-width: 1213px) 100vw, 1213px" /> </figure> 

300 LoginVSI users in this workload on Server 2008R2. The separation between enabled and disabled is still very prominent.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Host-2008-100.png" alt="" class="wp-image-2854" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Host-2008-100.png 1218w, http://theorypc.ca/wp-content/uploads/2018/09/Host-2008-100-300x123.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Host-2008-100-768x316.png 768w" sizes="(max-width: 1218px) 100vw, 1218px" /> </figure> 

100 LoginVSI users in this workload on Server 2008R2. I only captured one run and the low utilization makes the difference barely noticeable.

Some interesting results for sure. I took the data and put it into a pivot table to highlight the CPU differences for each workload against each operating system.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/CPUDifference.png" alt="" class="wp-image-2855" srcset="http://theorypc.ca/wp-content/uploads/2018/09/CPUDifference.png 996w, http://theorypc.ca/wp-content/uploads/2018/09/CPUDifference-300x168.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/CPUDifference-768x431.png 768w" sizes="(max-width: 996px) 100vw, 996px" /> </figure> 

This chart hightlights the difference in CPU percentage between mitigation enabled and disabled systems. The raw data:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/CPUDifferenceData.png" alt="" class="wp-image-2856" /> </figure> 

Again, interesting results. 2008R2 seems to have the largest average CPU seperation, hitting 14%, followed by 2012R2 at 11% and than 2016 having a difference of 4%.

One of things about these results is that they highlight the "headroom" of the operating systems. 2008R2 actually consumes _less_ CPU and so it has more _room_ for separation between the 3 tiers. On the 2016, there is so much time spent where the CPU was pegged at 100% for both types of host that makes a difference of "0%". So although the smaller number on server 2016 may lead you to believe it's better, _**it's actually not**__._<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compare.png" alt="" class="wp-image-2857" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compare.png 1181w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compare-300x164.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compare-768x419.png 768w" sizes="(max-width: 1181px) 100vw, 1181px" /> </figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compareData.png" alt="" class="wp-image-2858" srcset="http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compareData.png 574w, http://theorypc.ca/wp-content/uploads/2018/09/Hosts-CPU-workload_compareData-300x136.png 300w" sizes="(max-width: 574px) 100vw, 574px" /></figure> 

This shows it a little more clear. With mitigations ***enabled***, Server 2008R2 can do 500 users at <span style="text-decoration: underline;">less average CPU load</span> than 2016 can do 300 users with mitigations ***disabled***.

From the get-go, Server 2016 appears to consume 2x more CPU than Server 2008R2 in all non-capped scenarios with Server 2012 somewhere in between.

When we compare the operating systems against the different user counts we see the impact the operating system choice has on resources. 

### Mitigation Disabled:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/100Users_Disabled.png" alt="" class="wp-image-2860" srcset="http://theorypc.ca/wp-content/uploads/2018/09/100Users_Disabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/100Users_Disabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/100Users_Disabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /> <figcaption>100 Users</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/09/300Users_Disabled.png" alt="" class="wp-image-2862" srcset="http://theorypc.ca/wp-content/uploads/2018/09/300Users_Disabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/300Users_Disabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/300Users_Disabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /><figcaption>300 Users</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/09/500Users_Disabled.png" alt="" class="wp-image-2864" srcset="http://theorypc.ca/wp-content/uploads/2018/09/500Users_Disabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/500Users_Disabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/500Users_Disabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /><figcaption>500 Users</figcaption></figure> 

### Mitigation Enabled:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/09/100Users_Enabled.png" alt="" class="wp-image-2861" srcset="http://theorypc.ca/wp-content/uploads/2018/09/100Users_Enabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/100Users_Enabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/100Users_Enabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /> <figcaption>100 Users</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/09/300Users_Enabled.png" alt="" class="wp-image-2863" srcset="http://theorypc.ca/wp-content/uploads/2018/09/300Users_Enabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/300Users_Enabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/300Users_Enabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /><figcaption>300 Users</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/09/500Users_Enabled.png" alt="" class="wp-image-2865" srcset="http://theorypc.ca/wp-content/uploads/2018/09/500Users_Enabled.png 1221w, http://theorypc.ca/wp-content/uploads/2018/09/500Users_Enabled-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2018/09/500Users_Enabled-768x319.png 768w" sizes="(max-width: 1221px) 100vw, 1221px" /><figcaption>500 Users</figcaption></figure> 

## Final Word

Microsoft stated that they expected to see less of an impact of the Spectre/Meltdown mitigations with newer operating systems. Indeed this does turn out to be the case. However, the additional resource cost of newer operating systems is actually **\*more\*** than running 2008R2 or 2012R2 _with mitigations **enabled**_ . So if you're environment is sized for running Server 2016, you probably have infrastructure that has already been spec'ed for the much heavier OS anyways. If your infrastructure has been spec'ed for the older OS than you will see a larger impact. However, if you've spec'ed for the larger OS (say for a migration activity) but are running your older OS's on that hardware, you will see an impact **_but it will be less than when you go live with 2016_**.

Previously I had stated that there are two different, important performance mechanisms to consider; capacity and how fast work actually gets done. All of these simulated measurements are about capacity. I hope to see how speed is impacted between the OS's, but that may have to wait for a future posting. 

Tabulating the LoginVSI simulated results without ControlUp Insights has taken me weeks to properly format the results.  I was able to use a trial of ControlUp Insights to look at the real world impact of our existing applications and workloads. If my organization ever purchases Insights I would have had this post up a long time ago with even more data, looking at things like the storage subsystems. Hopefully we acquire this product in the future and if you want to save yourself and your organization time, energy and effort getting precise, accurate data that can be compared against scenarios you create: get ControlUp Insights.

  
I'm going to harp on this, but **YOUR WORKLOAD MATTERS MORE** than these simulated results. During this exercise, I was able to determine with ControlUp Insights that one of our applications is so light that we can host 1,000 users from a single host where that host struggled with 200 LoginVSI users. So **WORKLOAD MATTERS**. Just something to keep in mind when reviewing these results. LoginVSI produces results that can serve as a proxy for what you can expect if you can properly relate these results to your existing workload. LoginVSI also offers the capability to produce custom workloads tailored to your specific environment or applications so you can gauge impact with much more precision. 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
