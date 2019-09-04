---
id: 2717
title: Corrupt Registry Repair with Citrix Provisioning Services
date: 2018-04-02T22:23:48-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2018/04/02/2696-revision-v1/
permalink: /2018/04/02/2696-revision-v1/
---
I encountered an interesting issue and worked through a solution with a corrupt registry. Â The issue seemed innocuously enough, we upgraded PowerShell to 5.1. Â Upon reboot I encountered a bluescreen with code 0xF4:

<img class="aligncenter size-full wp-image-2700" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.43.37-PM.png" alt="" width="646" height="483" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.43.37-PM.png 646w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.43.37-PM-300x224.png 300w" sizes="(max-width: 646px) 100vw, 646px" /> 

I have encountered this issue before, but I haven&#8217;t recorded my troubleshooting steps until now.

Since this was a PVS target device, the easy method was deleting the version and trying to upgrade PowerShell to 5.1, which resulted in the same BSOD. Â So it was easily reproducible. Â So I tried it a few more times, because, why not?

I then deleted this version, and booted into the system and looked at Event Viewer for hints of what could be at fault. Â Going through it was pretty obvious that it was registry corruption:

<img class="aligncenter size-full wp-image-2697" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.16.16-PM.png" alt="" width="1077" height="300" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.16.16-PM.png 1077w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.16.16-PM-300x84.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.16.16-PM-768x214.png 768w" sizes="(max-width: 1077px) 100vw, 1077px" /> 

Filtering for EventID 5 shows all the attempts of booting to BSOD:

<img class="aligncenter size-full wp-image-2699" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.17.00-PM.png" alt="" width="521" height="209" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.17.00-PM.png 521w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.17.00-PM-300x120.png 300w" sizes="(max-width: 521px) 100vw, 521px" /> 

&nbsp;

I mean, it literally says the Registry was corrupted ðŸ™‚

Where can you find this corruption? Â With PVS it&#8217;s fairly simple but I believe the same process can exist for other systems including physical. Â The first step was to mount the vDisk to a VM.

<img class="aligncenter size-large wp-image-2702" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.22-PM.png" alt="" width="923" height="747" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.22-PM.png 923w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.22-PM-300x243.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.22-PM-768x622.png 768w" sizes="(max-width: 923px) 100vw, 923px" /> 

<img class="aligncenter size-full wp-image-2701" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.41-PM.png" alt="" width="922" height="745" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.41-PM.png 922w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.41-PM-300x242.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.47.41-PM-768x621.png 768w" sizes="(max-width: 922px) 100vw, 922px" /> 

&nbsp;

Mount the registry hive you suspect with corruption.

<img class="aligncenter size-full wp-image-2703" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.49.56-PM.png" alt="" width="344" height="306" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.49.56-PM.png 344w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.49.56-PM-300x267.png 300w" sizes="(max-width: 344px) 100vw, 344px" /> 

<img class="aligncenter size-large wp-image-2705" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.38-PM.png" alt="" width="786" height="478" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.38-PM.png 786w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.38-PM-300x182.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.38-PM-768x467.png 768w" sizes="(max-width: 786px) 100vw, 786px" /> 

<img class="aligncenter size-full wp-image-2704" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.49-PM.png" alt="" width="389" height="123" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.49-PM.png 389w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.50.49-PM-300x95.png 300w" sizes="(max-width: 389px) 100vw, 389px" /> 

<img class="aligncenter size-full wp-image-2707" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.52.06-PM.png" alt="" width="216" height="241" /> 

Next is to scan the registry for corruption. Â Thus far, I&#8217;ve only found corruption to be detectable if it&#8217;s a key or value that cannot be read. Â If the data on a value can be read but contains garbage it&#8217;s much harder to detect. Â In order to avoid permissions being a problem, I open a PowerShell prompt as SYSTEM using PSEXEC. Â If you don&#8217;t elevate permissions, some keys maybe restricted from the Admins group and this will be detected as a failure.

<img class="aligncenter size-full wp-image-2708" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.58.46-PM.png" alt="" width="556" height="288" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.58.46-PM.png 556w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-9.58.46-PM-300x155.png 300w" sizes="(max-width: 556px) 100vw, 556px" /> 

Once at this stage, it&#8217;s a one-liner to scan the registry:

<pre class="lang:ps decode:true">ls -Recurse -ErrorAction Stop | Out-File C:\RegCheck.txt</pre>

In my experience, corruption can be detected as &#8220;Permission Denied&#8221;, &#8220;Access Denied&#8221;,&#8221;Path does not exist&#8221; or some such:

<img class="aligncenter size-full wp-image-2709" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.05.20-PM.png" alt="" width="809" height="105" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.05.20-PM.png 809w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.05.20-PM-300x39.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.05.20-PM-768x100.png 768w" sizes="(max-width: 809px) 100vw, 809px" /> 

&nbsp;

At this point you can examine the text file to see the last path it explored:

<img class="aligncenter size-full wp-image-2710" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.06.33-PM.png" alt="" width="794" height="596" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.06.33-PM.png 794w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.06.33-PM-300x225.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.06.33-PM-768x576.png 768w" sizes="(max-width: 794px) 100vw, 794px" /> 

&nbsp;

At this point you can open regedit (as SYSTEM) and examine the keys within that path. Â Clicking through each one revealed the corrupted key:

<img class="aligncenter size-full wp-image-2711" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.08.43-PM.png" alt="" width="742" height="359" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.08.43-PM.png 742w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.08.43-PM-300x145.png 300w" sizes="(max-width: 742px) 100vw, 742px" /> 

Attempting to get Permissions on this key reveals it also exists on the ACL level:

<img class="aligncenter size-large wp-image-2713" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.43-PM.png" alt="" width="301" height="238" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.43-PM.png 301w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.43-PM-300x237.png 300w" sizes="(max-width: 301px) 100vw, 301px" /> 

<img class="aligncenter size-full wp-image-2712" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.51-PM.png" alt="" width="374" height="453" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.51-PM.png 374w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.10.51-PM-248x300.png 248w" sizes="(max-width: 374px) 100vw, 374px" /> 

&#8220;The requested security information is either unavailable or can&#8217;t be displayed&#8221;.

Deleting the key may fail as well:

<img class="aligncenter size-full wp-image-2714" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.12.40-PM.png" alt="" width="365" height="125" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.12.40-PM.png 365w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.12.40-PM-300x103.png 300w" sizes="(max-width: 365px) 100vw, 365px" /> 

At this point you need to evaluate how to manage the corruption. Â If you cannot delete the key, rename it, or in some way replace it you may have an option like I did&#8230; Â You can rename a higher up branch in the tree, go to a existing system with the same keys (with PVS I can go to the previous version and export that tree) and reimport.

<img class="aligncenter size-full wp-image-2715" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.18.37-PM.png" alt="" width="661" height="1220" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.18.37-PM.png 661w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-02-at-10.18.37-PM-163x300.png 163w" sizes="(max-width: 661px) 100vw, 661px" /> 

Unload the hive and boot up the system &#8211;> and you may have a fully working system!

I&#8217;ve used this trick here, and on a corrupt COMPONENTS hive in the past. Â With the COMPONENTS hive I got lucky I could replace the corrupted keys with ones from a branched vDisk. Â Other machines didn&#8217;t have the same key in COMPONENTS so I got lucky.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->