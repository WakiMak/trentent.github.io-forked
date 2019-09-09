---
id: 2647
title: Meltdown - Performance Impact Evaluation (Citrix & 6.5)
date: 2018-01-15T15:49:41-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2647
permalink: /2018/01/15/meltdown-performance-impact-evaluation-citrix-&-6-5/
image: /wp-content/uploads/2018/01/F6BF1FFB-9948-434F-8C2B-9392F496D102.png
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - Performance
  - VMWare

---
Meltdown came out and it's a vulnerability whose fix may have a performance impact. Â Microsoft has stipulated that the impact will be more severe if you:

a) Are an older OS  
b) Are using an older processor  
c) If your application utilizes lots of context switches

Unfortunately, the environment we are operating hits all of these nails on the head. Â We are using, I believe, the oldest OS that Microsoft is patching this for. Â We are using older processors from 2011-2013 which do not have the PCID optimization (as reported by the [SpeculationControl](https://gallery.technet.microsoft.com/scriptcenter/Speculation-Control-e36f0050) test script) which means performance is impacted even more.

I'm in a large environment where we have the ability to shuffle VM's around hosts and put VM's on specific hosts. Â This will allow us to measure the impact of Meltdown in its entirety. Â Our clusters are dedicated to Citrix & 6.5 servers.

Looking at our cluster and all of the Citrix & VM's, we have some VM's that are 'application siloed' - that is they only host a single application and some VM's that are 'generic'.

In order to determine the impact I looked at our cluster, summed up the total of each type of VM and then just divided by the number of hosts. Â We have 3 different geographical areas that have different VM types and user loads. Â I am going to examine each of these workload types across the different geographical areas and see what are the outcomes.

Since Meltdown impacts applications and workloads that have lots of context switches I used perfmon each server to record the context switches of each server.

[<img class="aligncenter wp-image-2649 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-11-at-9.35.21-AM-1.png" alt="" width="1154" height="792" srcset="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-11-at-9.35.21-AM-1.png 1154w, http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-11-at-9.35.21-AM-1-300x206.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-11-at-9.35.21-AM-1-768x527.png 768w" sizes="(max-width: 1154px) 100vw, 1154px" />](http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-11-at-9.35.21-AM-1.png)

The metrics I am interested in are the context switch values as they've been identified as the element that highlight the impact. Â My workloads look like this:

[<img class="aligncenter wp-image-2682 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/Chart-2.png" alt="" width="534" height="465" srcset="http://theorypc.ca/wp-content/uploads/2018/01/Chart-2.png 534w, http://theorypc.ca/wp-content/uploads/2018/01/Chart-2-300x261.png 300w" sizes="(max-width: 534px) 100vw, 534px" />](http://theorypc.ca/wp-content/uploads/2018/01/Chart-2.png)

Based on this chart, our largest impact should be Location B, followed by Location A, last with Location C.

However, the processors for each location are as follows:

Location A: Intel Xeon 2680  
Location B: Intel Xeon 2650 v2  
Location C: Intel Xeon 2680

The processors may play a roll as newer generation processors are supposed to fair better.

In order to test Meltdown in a side-by-side comparison of systems with and without the mitigation I took two identical hosts and populated them with an identical amount and type of servers. On one host we patched all the VM's with the mitigation and with the other host we left the VM's without the patches.

Using the wonderful [ControlUp Console](https://www.controlup.com), we can compare the results in their real-time dashboard. Â Unfortunately, the dashboard only gives us a "real time" view, ControlUp offers a product called "Insights" that can show the historical data, our organization has not subscribed to this product and so I've had to try and track the performance by exporting the ControlUp views on a interval and then manually sorting and presenting the data. Â Using the Insights view would be much, much faster.

ControlUp has 3 different views I was hoping to explore. Â The first view is the **hosts** view, this will be performance metrics pulled directly from the VMWare Host. Â The second view will be the computers view, and the last will be the sessions view. Â The computers and sessions view are metrics pulled directly from the Windows server itself. Â However, I am unable to accurately judge the performance of Windows Server metrics because of how it measures CPU performance.

Another wonderful thing about ControlUp is we can logically group our VM's into folders, from there ControlUp can sum the values and present it in an easily digestible presentation. Â I created a logical structure like so and populated my VM's:

[<img class="aligncenter wp-image-2653 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/VMs.png" alt="" width="308" height="762" srcset="http://theorypc.ca/wp-content/uploads/2018/01/VMs.png 308w, http://theorypc.ca/wp-content/uploads/2018/01/VMs-121x300.png 121w" sizes="(max-width: 308px) 100vw, 308px" />](http://theorypc.ca/wp-content/uploads/2018/01/VMs.png)

&nbsp;

And then within ControlUp we can "focus" on each "Location" folder and if we select the "Folder" view it presents the sums of the logical view.

# HOSTS

[<img class="aligncenter wp-image-2651 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/hostsNorth.png" alt="" width="1143" height="127" srcset="http://theorypc.ca/wp-content/uploads/2018/01/hostsNorth.png 1143w, http://theorypc.ca/wp-content/uploads/2018/01/hostsNorth-300x33.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/hostsNorth-768x85.png 768w" sizes="(max-width: 1143px) 100vw, 1143px" />](http://theorypc.ca/wp-content/uploads/2018/01/hostsNorth.png)

[<img class="aligncenter wp-image-2650 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/hosts.png" alt="" width="1146" height="130" srcset="http://theorypc.ca/wp-content/uploads/2018/01/hosts.png 1146w, http://theorypc.ca/wp-content/uploads/2018/01/hosts-300x34.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/hosts-768x87.png 768w" sizes="(max-width: 1146px) 100vw, 1146px" />](http://theorypc.ca/wp-content/uploads/2018/01/hosts.png)

[<img class="aligncenter wp-image-2652 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/hostsSouth.png" alt="" width="1143" height="128" srcset="http://theorypc.ca/wp-content/uploads/2018/01/hostsSouth.png 1143w, http://theorypc.ca/wp-content/uploads/2018/01/hostsSouth-300x34.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/hostsSouth-768x86.png 768w" sizes="(max-width: 1143px) 100vw, 1143px" />](http://theorypc.ca/wp-content/uploads/2018/01/hostsSouth.png)

In the hosts view, we can very quickly we can see impact, ranging from 5%-26%. Â However, this is a realtime snapshot, I tracked the average view and examined only the "business hours" as our load is VERY focused on the 8AM-4PM. Â After these hours and we see a significant drop in our load. Â If the servers are not being stressed the performance seems to be a lot more even or not noticeable (in a cumulative sense).

# FOLDERS

[<img class="aligncenter wp-image-2656 size-large" src="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewC-1600x129.png" alt="" width="1140" height="92" srcset="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewC-1600x129.png 1600w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewC-300x24.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewC-768x62.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />](http://theorypc.ca/wp-content/uploads/2018/01/FolderViewC.png)

[<img class="aligncenter wp-image-2654 size-large" src="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewB-1600x140.png" alt="" width="1140" height="100" srcset="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewB-1600x140.png 1600w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewB-300x26.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewB-768x67.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />](http://theorypc.ca/wp-content/uploads/2018/01/FolderViewB.png)

[<img class="aligncenter wp-image-2655 size-large" src="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewA-1600x109.png" alt="" width="1140" height="78" srcset="http://theorypc.ca/wp-content/uploads/2018/01/FolderViewA-1600x109.png 1600w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewA-300x21.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/FolderViewA-768x53.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" />](http://theorypc.ca/wp-content/uploads/2018/01/FolderViewA.png)

Some interesting results. Â We are consistently seeing longer login times and application launch times. Â 2/3 of the environments have lower user counts on the unpatched servers with the Citrix load balancing making that determination. Â The one environment that had more users on the mitigation servers are actually our least loaded servers in terms of servers per host and users per server, so it's possible that more users would drive into a gap, but as of now it shows that one of our environment can support an equal number of users.

Examining this view and the historical data presented I encountered oddities - namely the CPU utilization seemed to be fairly even more often than not, but the hosts view showed greater separation between the machines with mitigation and without. Â I started to [explore why and believe I may have come across this issue previously](https://theorypc.ca/2015/07/23/citrix-seamless-flags-and-their-impact/).

2008R2-era servers [have less accurate reporting of CPU utilization.](https://www.google.ca/url?sa=t&rct=j&q=&esrc=s&source=web&cd=3&cad=rja&uact=8&ved=0CCoQFjACahUKEwiB5ZnruPLGAhUJKogKHQ43CDY&url=http%3A%2F%2Fmedia.ch9.ms%2Fteched%2Fna%2F2011%2Fppt%2FWCL304.pptx&ei=xX2xVcHuPInUoASO7qCwAw&usg=AFQjCNFCNa8W9sfmksOfP5Pr0A39vkzocw&sig2=-eIWfdvo0rBV0M1fktDzsA&bvm=bv.98476267,d.cGU)

I believe this is also the API that ControlUp is using with these servers to report on usage. Â When I was examining a single server with process explorer I noticed a \*minimum\* CPU utilization of 6%, but task manager and ControlUp would report 0% utilization at various points. Â The issue is an accuracy, adding and rounding issue. Â The more users on a server with more processes and those processes consuming ever so slightly CPU, the more the inaccuracy. Â Example:

<div id="attachment_2657" style="width: 593px" class="wp-caption aligncenter">
  <a href="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-2.32.42-PM.png"><img aria-describedby="caption-attachment-2657" class="wp-image-2657 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-2.32.42-PM.png" alt="" width="583" height="1074" srcset="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-2.32.42-PM.png 583w, http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-2.32.42-PM-163x300.png 163w" sizes="(max-width: 583px) 100vw, 583px" /></a></p> 
  
  <p id="caption-attachment-2657" class="wp-caption-text">
    Left, Task Manager. Right, Process Explorer
  </p>
</div>

We have servers with hundreds of users utilizing a workflow like this where they are using just a fraction of a percent of CPU resources. Â Taskmanager and the like will not catch these values and round <span style="text-decoration: underline;"><strong>*down*</strong></span>. Â If you have 100 users using a process that consumes 0.4% CPU then our inaccuracy is in the 40% scale! Â So relying on the VM metrics of ControlUp or Windows itself is not helpful. Â Unfortunately, this destroys my ability to capture information within the VM, requiring us to solely rely on the information within VMWare. Â To be clear, I doÂ _**NOT**_ believe Windows 2012 R2 and greater OS's have this discrepancy (although I have not tested) so this issue manifests itself pretty viciously in the & 6.5 -era servers. Â Essentially, if Meltdown is increasing CPU times on processes by a fraction of a percent then Windows will report as if everything is ok and you will probably not actually notice or think there is an issue!

In order to try and determine if this impact is detectable I have two servers with the same base image, with one having the mitigation installed and other did not. Â I used process explorer and "saved" the process list over the course of a few hours. Â I ensured the servers had a similar amount of users using a specific application that only presented a specific workload so everything was as similar as possible. Â In addition, I looked at processes that aren't configurable (or since the servers have the same base image they are configured identically). Â Here were my results:

[<img class="aligncenter wp-image-2658 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/winlogon.png" alt="" width="901" height="630" srcset="http://theorypc.ca/wp-content/uploads/2018/01/winlogon.png 901w, http://theorypc.ca/wp-content/uploads/2018/01/winlogon-300x210.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/winlogon-768x537.png 768w" sizes="(max-width: 901px) 100vw, 901px" />](http://theorypc.ca/wp-content/uploads/2018/01/winlogon.png)

Just eye balling it, it appears that the mitigation has had an impact on the fractional level. Â When taking the average of the Winlogon.exe and iexplore.exe processes into account:

[<img class="aligncenter wp-image-2659 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-3.00.47-PM.png" alt="" width="242" height="50" />](http://theorypc.ca/wp-content/uploads/2018/01/Screen-Shot-2018-01-14-at-3.00.47-PM.png)

&nbsp;

These numbers may seem small, but once you start considering the number of users the amount wasted grows dramatically. Â For 100 users, winlogon.exe goes from consuming a total of 1.6% to 7.1%Â of the CPU resulting in an additional load of 5.5%. Â The iexplore.exe is even more egregious as it spawns 2 processes per user, and these averages are per process. Â For 100 users, 200 iexplore.exe processes will be spawned. Â The iexplore.exe CPU utilization goes from 15.6% to 38.8%, for an additional load of 23.2%. Â Adding the mitigation patch can impact our load pretty dramatically,Â _<span style="text-decoration: underline;">even though it may be under reported thus impacting users on a far greater scale by adding more users to servers that don't have the resources that Windows is reporting it has</span>.Â_ For an application like IE, this may just mean greater slowness - depending on the workload/workflow - but if you have an application more sensitive to these performance scenarios your users may experience slowness even though the servers themselves look OK from most (all?) reporting tools.

Continuing with the HOSTS view, I exported all data ControlUp collects on a minute interval and then added the data to Excel and created my pivot tables with the hosts that are hosting servers with the mitigation patches and the ones without.Â This is what I saw for Saturday-Sunday, these days are lightly loaded.

[<img class="aligncenter wp-image-2660 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Hosts.png" alt="" width="1569" height="837" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Hosts.png 1569w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Hosts-300x160.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Hosts-768x410.png 768w" sizes="(max-width: 1569px) 100vw, 1569px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Hosts.png)

This is **Location B**, the host with VM's that are unpatched is in orange and the host with patched VM's is in blue.Â the numbers are pretty identical when CPU utilization on the host is around or below 10%, but once it starts to get loaded the separation becomes apparent.

Since these datapoints were every minute, I used a moving average of 20 data points (3 per hour) to present the data in a cleaner way:

[<img class="aligncenter wp-image-2661 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/smoothedOut.png" alt="" width="1467" height="839" srcset="http://theorypc.ca/wp-content/uploads/2018/01/smoothedOut.png 1467w, http://theorypc.ca/wp-content/uploads/2018/01/smoothedOut-300x172.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/smoothedOut-768x439.png 768w" sizes="(max-width: 1467px) 100vw, 1467px" />](http://theorypc.ca/wp-content/uploads/2018/01/smoothedOut.png)

Looking at the data for this Monday morning, we see the following:

## Location B

[<img class="aligncenter wp-image-2663 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Monday.png" alt="" width="1472" height="835" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Monday.png 1472w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Monday-300x170.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Monday-768x436.png 768w" sizes="(max-width: 1472px) 100vw, 1472px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationB_Monday.png)

&nbsp;

Some interesting events, at 2:00AM the VM's reboot.Â We reboot odd and even servers each day, and in my organization of testing this, I put all the odd VM's on the blue host, and the even VM's on the orange host.Â So the blue line going up at 2:00AM is the odd (patched) VM's rebooting.Â The reboot cycle is staggered to take place over a 90 minute interval (last VM's should reboot around 3:30AM).Â After the reboot, the servers come up and do some "pre-user" startup work like loading AppV packages, App-V registry prestaging, etc.Â I track the App-V registry pre-staging duration during bootup and here are my results:

[<img class="aligncenter wp-image-2665 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/RegistryPerformance-1.png" alt="" width="734" height="639" srcset="http://theorypc.ca/wp-content/uploads/2018/01/RegistryPerformance-1.png 734w, http://theorypc.ca/wp-content/uploads/2018/01/RegistryPerformance-1-300x261.png 300w" sizes="(max-width: 734px) 100vw, 734px" />](http://theorypc.ca/wp-content/uploads/2018/01/RegistryPerformance-1.png)

Registry Pre-staging in AppV is a light-Read heavy-Write exercise.Â Registry reading and writing are slow in 2008R2 and our time to execute this task went from 610 seconds to 693 seconds for an overall duration increase of 14%.

Looking at Location A and C

## Location A

[<img class="aligncenter wp-image-2666 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_Hosts.png" alt="" width="1443" height="674" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_Hosts.png 1443w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_Hosts-300x140.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_Hosts-768x359.png 768w" sizes="(max-width: 1443px) 100vw, 1443px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationA_Hosts.png)

## 

## Location C (under construction)

[<img class="aligncenter wp-image-2668 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationC_Hosts-1.png" alt="" width="1445" height="517" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationC_Hosts-1.png 1445w, http://theorypc.ca/wp-content/uploads/2018/01/LocationC_Hosts-1-300x107.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationC_Hosts-1-768x275.png 768w" sizes="(max-width: 1445px) 100vw, 1445px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationC_Hosts-1.png)

We can see in Location A the CPU load is pretty similar until the 20% mark, then separation starts to ramp up fairly drastically.Â For Location C, unfortunately, we are undergoing maintenance on the 'patched' VM's, so I'm showing this data for transparency but it's only relevant up to the 14th.Â I'll update this in the next few days when the 'patched' VM's come back online.

Now, I'm going to look at how "Windows" is reporting CPU performance vs the Hosts CPU utilization.

### Location A

[<img class="aligncenter wp-image-2670 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_WindowsCPU_vs_Hosts-1.png" alt="" width="1334" height="669" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_WindowsCPU_vs_Hosts-1.png 1334w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_WindowsCPU_vs_Hosts-1-300x150.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_WindowsCPU_vs_Hosts-1-768x385.png 768w" sizes="(max-width: 1334px) 100vw, 1334px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationA_WindowsCPU_vs_Hosts-1.png)

&nbsp;

### Location B

[<img class="aligncenter wp-image-2671 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_WindowsCPU_vs_Hosts.png" alt="" width="1394" height="668" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_WindowsCPU_vs_Hosts.png 1394w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_WindowsCPU_vs_Hosts-300x144.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_WindowsCPU_vs_Hosts-768x368.png 768w" sizes="(max-width: 1394px) 100vw, 1394px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationB_WindowsCPU_vs_Hosts.png)

&nbsp;

The information this is conveying is to NOT TRUST the Windows CPU utilization meter (at least 2008 R2).Â The CPU Utilization on the VM-level does not appear to reflect the load on the hosts.Â While the VM's with the patch and without the patch both report nearly identical levels of CPU utilization, on the host level the spread is much more dramatic.

&nbsp;

Lastly, I am able to pull some other metrics that ControlUp tracks.Â Namely, Logon Duration and Application Launch duration.Â For each of the locations I got a report of the difference between the two environments

#### Location A: Average Application Load Time

[<img class="aligncenter wp-image-2672 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_ApplicationLoadTimes.png" alt="" width="1070" height="812" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_ApplicationLoadTimes.png 1070w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_ApplicationLoadTimes-300x228.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_ApplicationLoadTimes-768x583.png 768w" sizes="(max-width: 1070px) 100vw, 1070px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationA_ApplicationLoadTimes.png)

#### Location B: Average Application Load Time

[<img class="aligncenter wp-image-2673 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_ApplicationLoadTimes.png" alt="" width="1092" height="816" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_ApplicationLoadTimes.png 1092w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_ApplicationLoadTimes-300x224.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_ApplicationLoadTimes-768x574.png 768w" sizes="(max-width: 1092px) 100vw, 1092px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationB_ApplicationLoadTimes.png)

&nbsp;

#### Location A: Logon Duration

[<img class="aligncenter wp-image-2675 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration.png" alt="" width="814" height="817" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration.png 814w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration-300x300.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration-768x771.png 768w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration-100x100.png 100w" sizes="(max-width: 814px) 100vw, 814px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationA_LogonDuration.png)

&nbsp;

#### Location B: Logon Duration

[<img class="aligncenter wp-image-2674 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration.png" alt="" width="825" height="820" srcset="http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration.png 825w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration-300x298.png 300w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration-768x763.png 768w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration-100x100.png 100w" sizes="(max-width: 825px) 100vw, 825px" />](http://theorypc.ca/wp-content/uploads/2018/01/LocationB_LogonDuration.png)

&nbsp;

&nbsp;

&nbsp;

In each of the metrics recorded we experience a worsening experience for our user base, from the application taking longer to launch, to logon times increasing.

# What does this all mean?

In the end, <span style="text-decoration: underline;"><strong>Meltdown</strong> </span>has a significant impact on our Citrix & 6.5 environment.Â The perfect storm of older CPU's, an older OS and applications that have workflows that are impacted by the patch means our environment is grossly impacted.Â Location A has a maximum hit (as of today) of **21%**.Â Location B having a spread of **12%**.Â I had originally predicted that Location B would have the largest impact, however the newer V2 processors may be playing a roll and the performance of the V2 processors maybe more efficient than the older 2680.

In the end, the performance hit is not insignificant and reduces our capacity significantly once these patches are deployed.Â I plan on adding new articles once I have more data on Meltdown and then further again once we start adding the mitigation's against Spectre.

<div id="attachment_2676" style="width: 744px" class="wp-caption aligncenter">
  <a href="http://theorypc.ca/wp-content/uploads/2018/01/Host_CPUUtilization_LocationAB.png"><img aria-describedby="caption-attachment-2676" class="wp-image-2676 size-full" src="http://theorypc.ca/wp-content/uploads/2018/01/Host_CPUUtilization_LocationAB.png" alt="" width="734" height="879" srcset="http://theorypc.ca/wp-content/uploads/2018/01/Host_CPUUtilization_LocationAB.png 734w, http://theorypc.ca/wp-content/uploads/2018/01/Host_CPUUtilization_LocationAB-251x300.png 251w" sizes="(max-width: 734px) 100vw, 734px" /></a></p> 
  
  <p id="caption-attachment-2676" class="wp-caption-text">
    CPU Utilization on the hosts. Orange is a host with VM's without the Meltdown patches, blue is with the patches.
  </p>
</div>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->