---
id: 2529
title: Citrix Provisioning Server - PXE requests stop working
date: 2017-08-10T11:10:36-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2529
permalink: /2017/08/10/citrix-provisioning-server-pxe-requests-stop-working/
image: /wp-content/uploads/2017/08/Icon.png
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - Performance
  - Provisioning Services
---
We use a bootable ISO in our environment to boot our VM's to a specific set of PVS servers. Â This ISO will vary by region enforcing that each target device that boots will be directed to their closest PVS server.

However, we have 1 region that does not leverage this capability and this region was designed to utilize the PXE services of the Citrix PVS server's. Â Occasionally, we encountered VM's that will not boot and instead the console shows "PXE-E53: no boot filename received"

<img class="aligncenter size-full wp-image-2530" src="http://theorypc.ca/wp-content/uploads/2017/08/PXE-E53.png" alt="" width="842" height="469" srcset="http://theorypc.ca/wp-content/uploads/2017/08/PXE-E53.png 842w, http://theorypc.ca/wp-content/uploads/2017/08/PXE-E53-300x167.png 300w, http://theorypc.ca/wp-content/uploads/2017/08/PXE-E53-768x428.png 768w" sizes="(max-width: 842px) 100vw, 842px" /> 

&nbsp;

When I logged onto the Citrix PVS servers, I checked their services. Â Both services were reported as "Running":

<img class="aligncenter size-full wp-image-2531" src="http://theorypc.ca/wp-content/uploads/2017/08/Services.png" alt="" width="808" height="228" srcset="http://theorypc.ca/wp-content/uploads/2017/08/Services.png 808w, http://theorypc.ca/wp-content/uploads/2017/08/Services-300x85.png 300w, http://theorypc.ca/wp-content/uploads/2017/08/Services-768x217.png 768w" sizes="(max-width: 808px) 100vw, 808px" /> 

When I checked the event logs I did not see any errors in either the application log or the system log. Â Administrative events showed nothing out the usual either.

In order to confirm that the PVS service was actually listening, I executed

<pre class="lang:default decode:true ">netstat -ano</pre>

this showed me all the open ports the server was listening for and the processes tied to those ports. Â Since PXE is a UDP operation, I examined the UDP portion of the netstat output. Â<img class="aligncenter size-full wp-image-2532" src="http://theorypc.ca/wp-content/uploads/2017/08/port_69.png" alt="" width="789" height="317" srcset="http://theorypc.ca/wp-content/uploads/2017/08/port_69.png 789w, http://theorypc.ca/wp-content/uploads/2017/08/port_69-300x121.png 300w, http://theorypc.ca/wp-content/uploads/2017/08/port_69-768x309.png 768w" sizes="(max-width: 789px) 100vw, 789px" /> 

Port 69 is used by TFTP to transfer files, and port 67 is used by PXE. Â However, I only saw port 69, port 67 was no where to be found. Â I restarted the "Citrix PVS PXE Service", reran netstat and confirmed that the PXE port was not listening and matched up the process ID to the proper services.

<img class="aligncenter size-large wp-image-2533" src="http://theorypc.ca/wp-content/uploads/2017/08/a_match-1600x643.png" alt="" width="1140" height="458" srcset="http://theorypc.ca/wp-content/uploads/2017/08/a_match-1600x643.png 1600w, http://theorypc.ca/wp-content/uploads/2017/08/a_match-300x121.png 300w, http://theorypc.ca/wp-content/uploads/2017/08/a_match-768x309.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

Restarting the failed target devices and they began to boot properly.

However, why did this fail in the first place? Â I read on the Citrix forums that the Citrix services can become unbound if the network is not available when the services are started. Â To test this I rebooted one the affected Citrix PVS servers. Â Sure enough, it came back up with port 67 not being monitored but the service in a 'Running' state. Â I wanted to see if I could capture the flow of communication from the network and when the service started so I used procmon and enabled "Boot Logging".

Lo and behold, the procmon monitoring on startup added enough of a delay that the PXE service was bound consistently. Â Stopping the boot logging and the PXE service would start but fail to bind to the port.

So now this leads to a bit of a quandary. Â The delay seems to be in the milliseconds. Â I've considered a couple solutions for this issue.

  1. A startup script that checks to ensure both ports and restarts the proper service if one of the ports is not found.
  2. Change the service startup type to be "Automatic (Delayed Start)". Â This delays the service by up to 2 minutes. Â This does mean that the PVS server will NOT be able to service target device boot requests during this window.

I think we're going to go with option 2. Â The reason is we can apply this setting change via Group Policy Preferences. Â This ensures that if we any removal/upgrade of the PVS software this setting will get reapplied. Â And then we don't have to worry about upgrading the OS and losing the startup script either or maintaining a script.

We've been affected by this a few times in the past, the fix has always been to restart the PVS server, but I managed to hit a window where the failure was happening consistently and managed to get this information. Â 

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->