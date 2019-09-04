---
id: 1132
title: AppV 4.6SP2 lies about the registry
date: 2016-04-25T08:38:00-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/653-autosave-v1/
permalink: /2016/04/25/653-autosave-v1/
---
We recently had an application update and decided we were going to move the registry values outside the AppV bubble into the real world via GPO.

The values existed in the registry here:

<pre class="lang:batch decode:true ">HKLM\SOFTWARE\Application /v ServerName /d OLDSERVER</pre>

When I did the /exe cmd.exe trick for the application and opened the package I could see the registry keys resided in that location.Â I then deleted that registry key in the sequencer and set the registry value for Application to &#8220;Merge with local keys&#8221;.Â After doing so we made a GPO at this location:

<pre class="lang:default decode:true ">HKLM\SOFTWARE\Application /v ServerName /d NEWSERVER</pre>

And I happily launched the application expecting it to see the new server key and be off on its merry way.

It didn&#8217;t work.Â When I /exe cmd.exe and looked at the registry there was no ServerName key.Â Disappointed I resequenced the application on 2008 R2.Â Now, in the AppV sequencer I noticed that the keys were located in the HKLM\Software\Wow6432Node.Â I then installed the newly sequenced application and launched regedit in the bubble.Â Regedit showed me that the path was &#8220;HKLM\SOFTWARE\Application&#8221;

Adding Registry keys to HKLM\SOFTWARE\WOW6432Node\Application allowed them to be seen properly in the bubble.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->