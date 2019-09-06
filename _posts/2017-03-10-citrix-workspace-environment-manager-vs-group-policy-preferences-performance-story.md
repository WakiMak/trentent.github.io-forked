---
id: 2062
title: Citrix Workspace Environment Manager vs. Group Policy Preferences â€“ Performance Story
date: 2017-03-10T13:10:45-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2062
permalink: /2017/03/10/citrix-workspace-environment-manager-vs-group-policy-preferences-performance-story/
image: /wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.55.17-PM.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - Workspace Environment Manager
---
Part one:Â [Citrix Workspace Environment Manager - First Impressions](https://theorypc.ca/2017/03/02/citrix-workspace-environment-manager-first-impressions/)  
Part two: [Citrix Workspace Environment Manager - Second Impressions](https://theorypc.ca/2017/03/08/citrix-workspace-environment-manager-second-impressions/)

If you've been following me, I've been exploring using WEM to apply some registry values instead of using Group Policy Preference. Â WEM does things differently which requires some thinking on how to implement it. Â The lack of a Boolean OR value is the biggest draw back, but it is possible to get around it, although our environment hasn't required multiple AND OR AND OR AND OR statements, so all the settings migrations I have done were possible.

But the meat of this post is HOW quickly can WEM process registry entries vs. GPP.

In order to compare these two I've subscribed to an old standby - Procmon. Â I logged into one of my Citrix servers with another account, and started procmon. Â I then launched an application published on this server (notepad). Â I used [ControlUp](http://www.controlup.com) to measure the performance of the actual logon and group policy extension processing. Â The one we are particularly interested in is the Group Policy Registry entry. Â This measures the performance of the Group Policy Registry portion:

<img class="aligncenter size-full wp-image-2063" src="http://theorypc.ca/wp-content/uploads/2017/03/GP_Extension_Time.png" alt="" width="931" height="427" srcset="http://theorypc.ca/wp-content/uploads/2017/03/GP_Extension_Time.png 931w, http://theorypc.ca/wp-content/uploads/2017/03/GP_Extension_Time-300x138.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/GP_Extension_Time-768x352.png 768w" sizes="(max-width: 931px) 100vw, 931px" /> 

&nbsp;

**Group Policy Registry executed in <span style="text-decoration: underline;">3494ms</span>.** Â However, I have two GPO objects with values that were evaluated by this client side extension. Â For WEM I only migrated a single GPO so I'll have to focus on that single one for the CSE. Â To find the Group Policy engine, I used ControlUp to discover it via selecting svchost.exe's processes and discovering them. Â The PID was 1872:

<img class="aligncenter size-full wp-image-2064" src="http://theorypc.ca/wp-content/uploads/2017/03/PID.png" alt="" width="437" height="577" srcset="http://theorypc.ca/wp-content/uploads/2017/03/PID.png 437w, http://theorypc.ca/wp-content/uploads/2017/03/PID-227x300.png 227w" sizes="(max-width: 437px) 100vw, 437px" /> 

One of the cool things about procmon is being able to suss out time stamps exactly.

For the Group Policy Registry CSE I can see it was activated at exactly 2:33:12.2056862. Â From there it checks the group policy history for the XML file then compares it to the one in your sysvol:

<img class="aligncenter size-full wp-image-2065" src="http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE.png" alt="" width="1258" height="576" srcset="http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE.png 1258w, http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE-300x137.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE-768x352.png 768w" sizes="(max-width: 1258px) 100vw, 1258px" /> 

With this particular policy, we actually check to see if it's a 32bit or 64bit system, we check for various pieces of software to see if they're installed or not and we then apply registry keys based on those results. Â For instance:

<img class="aligncenter size-full wp-image-2066" src="http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE_PDFArchitech.png" alt="" width="1261" height="211" srcset="http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE_PDFArchitech.png 1261w, http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE_PDFArchitech-300x50.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/Procmon_CSE_PDFArchitech-768x129.png 768w" sizes="(max-width: 1261px) 100vw, 1261px" /> 

We have a GPP Registry values that are set via some item-level-targetting that are dependent on whether PDF architect is installed or not. Â You can literally see it check for PDF Architect and then set whatever values we determined need to be set by that result (ShowAssociationDialog,ShowPresentation, etc).

However cool this is, this GPO is not the one I want ðŸ™‚

I want the next GPO ({E6775312-...}). Â This GPO is the one that I have converted to WEM as it only dealt with group membership. Â WEM can filter on conditions like a file/folder exist but since I didn't want to do another thousand or so registry entries I focused on the smaller GPO.

<img class="aligncenter size-full wp-image-2067" src="http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat.png" alt="" width="1245" height="745" srcset="http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat.png 1245w, http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat-300x180.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat-768x460.png 768w, http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat-550x330.png 550w" sizes="(max-width: 1245px) 100vw, 1245px" /> 

&nbsp;

This is the real meat. Â We can see the GPO I'm interested in started processing at 2:33:14:5252890.

<img class="aligncenter size-full wp-image-2068" src="http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat_is_cooked.png" alt="" width="1394" height="357" srcset="http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat_is_cooked.png 1394w, http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat_is_cooked-300x77.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/the_real_meat_is_cooked-768x197.png 768w" sizes="(max-width: 1394px) 100vw, 1394px" /> 

And then completed at 2:33:15.2480580.

The CSE didn't actually finish though, until 2:33:15.706579 :

<img class="aligncenter size-full wp-image-2069" src="http://theorypc.ca/wp-content/uploads/2017/03/cleanup_complete_activities.png" alt="" width="1377" height="161" srcset="http://theorypc.ca/wp-content/uploads/2017/03/cleanup_complete_activities.png 1377w, http://theorypc.ca/wp-content/uploads/2017/03/cleanup_complete_activities-300x35.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/cleanup_complete_activities-768x90.png 768w" sizes="(max-width: 1377px) 100vw, 1377px" /> 

&nbsp;

It looks like it was finishing some RSOP stuff Â (RSOPLogging is disabled, BTW) storing things in the user registry hive like GPOLink, GPOName, etc. Â Either way, these actions add time to the CSE to complete. Â The total time spent in the Group Policy Registry CSE was:

<span style="text-decoration: underline;"><strong>~3501ms.</strong></span>

The total time reported by ControlUp was 3494ms. Â So I'm probably a bit off by the start/finish of the CSE but pretty goddamn close. Â The real meat of the GPO Registry processing (eg, the GPO I was actually concerned about) was:

<span style="text-decoration: underline;"><strong>1181ms</strong></span>.

&nbsp;

So how does WEM do?

One of the ways that WEM 'helps' counting stats is by pushing the processing into the user session where it can be processed asynchronously. Â WEM creates a log in your %userprofile% folder that you can examine to see when it starts:

<img class="aligncenter size-full wp-image-2072" src="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Yolo-1.png" alt="" width="904" height="400" srcset="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Yolo-1.png 904w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Yolo-1-300x133.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Yolo-1-768x340.png 768w" sizes="(max-width: 904px) 100vw, 904px" /> 

&nbsp;

Unfortunately, WEM's log isn't very granular. Â Procmon will fix that again ðŸ™‚

The entry I'm looking for in WEM is "MainController.ProcessRegistryValues()". Â This tells us when it starts doing the registry work:

<img class="aligncenter size-full wp-image-2073" src="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry-1.png" alt="" width="822" height="408" srcset="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry-1.png 822w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry-1-300x149.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry-1-768x381.png 768w" sizes="(max-width: 822px) 100vw, 822px" /> 

The processing started after 3:34:39. Â Procmon will help us zero in on that time:

<img class="aligncenter size-full wp-image-2074" src="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry_Processing.png" alt="" width="1382" height="395" srcset="http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry_Processing.png 1382w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry_Processing-300x86.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/WEM_Registry_Processing-768x220.png 768w" sizes="(max-width: 1382px) 100vw, 1382px" /> 

We can see pretty clearly that it starts applying registry values at 3:34:39.4995477.

It completes at...

<img class="aligncenter size-full wp-image-2075" src="http://theorypc.ca/wp-content/uploads/2017/03/last_registry_entry.png" alt="" width="1269" height="398" srcset="http://theorypc.ca/wp-content/uploads/2017/03/last_registry_entry.png 1269w, http://theorypc.ca/wp-content/uploads/2017/03/last_registry_entry-300x94.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/last_registry_entry-768x241.png 768w" sizes="(max-width: 1269px) 100vw, 1269px" /> 

<img class="aligncenter size-full wp-image-2076" src="http://theorypc.ca/wp-content/uploads/2017/03/procmon_WEM.png" alt="" width="1394" height="466" srcset="http://theorypc.ca/wp-content/uploads/2017/03/procmon_WEM.png 1394w, http://theorypc.ca/wp-content/uploads/2017/03/procmon_WEM-300x100.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/procmon_WEM-768x257.png 768w" sizes="(max-width: 1394px) 100vw, 1394px" /> 

3:34:46.5753350.

Total time:

<span style="text-decoration: underline;"><strong>7076ms</strong></span>.

Hmmm. Â About twice as slow. Â It is certainly possible that the WMI queries are what is killing my performance, but without a way to check the group membership of the server the user is on, I am hobbled. Â It's possible that if we were implementing WEM from the get go we could think of a naming scheme that would work better for us, with the limitations of the wildcard (although I think it's a bug in WEM as opposed to a poor naming scheme - just my opinion), but to rework our entire environment is not feasible.

In order to determine if WEM will perform better without the WQL queries, I manually edited the condition to be focused on 'Computer Name Match' and specified all the relevant servers.

The results?

<img class="aligncenter size-large wp-image-2079" src="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.48.16-PM-1600x260.png" alt="" width="1140" height="185" srcset="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.48.16-PM-1600x260.png 1600w, http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.48.16-PM-300x49.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.48.16-PM-768x125.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

Started processing: 12:37:56.029

<img class="aligncenter size-large wp-image-2080" src="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.49.46-PM-1600x279.png" alt="" width="1140" height="199" srcset="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.49.46-PM-1600x279.png 1600w, http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.49.46-PM-300x52.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-12.49.46-PM-768x134.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

Finished at 12:37:59.634

&nbsp;

Total time:Â <span style="text-decoration: underline;"><strong>3605ms</strong></span>.

So there is big savings without doing any WQL processing.

&nbsp;

# Final Results

<img class="aligncenter size-full wp-image-2084" src="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-1.18.36-PM.png" alt="" width="764" height="449" srcset="http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-1.18.36-PM.png 764w, http://theorypc.ca/wp-content/uploads/2017/03/Screen-Shot-2017-03-10-at-1.18.36-PM-300x176.png 300w" sizes="(max-width: 764px) 100vw, 764px" /> 

&nbsp;

&nbsp;

But it still doesn't compare to the Group Policy Preferences - Registry CSE. Â The speed of the CSE is still faster. Â Pretty significantly, actually. Â And there are other considerations you need to consider for WEM as well. Â It applies the registry values \*after\* you've logged in, whereas GPP does it before. Â This allows WEM to operate asynchronously and should reduce logon times but there is a drawback. Â And for us it's a big drawback. Â When it applies the registry values, for most of our apps they need to be in place \*before\* you launch the application. Â So setting the values after or while an application is launching may lead to some inconsistent experiences. Â For us,Â **this caveat only applies to a _&_** environment.

When talking about a XenDesktop environment a premium is generally placed on _**getting to the desktop**_ where a user has to navigate to an application to launch it, which would probably require enough time that WEM will be able to apply its required values before the user is able to launch their application. Â In this scenario, saving 3-4 seconds of the user waiting for their desktop to appear are valuable and WEM (mostly) solves this issue by pushing it asynchronously to the shell.

For &, we are still considering WEM to see how it performs with roaming users and having printers change for session reconnections; depending on their client name/ip space or some other variable. Â That investigation will come in the future. Â For now, it looks like we're going to keep using Group Policy Preferences for our registry application for &.

Stay tuned for some WEM gotcha's I've learned doing this exercise.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->