---
id: 969
title: 'AppV5 &#8211; Sequencing Logibec eClinibase'
date: 2016-04-24T16:01:53-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/527-autosave-v1/
permalink: /2016/04/24/527-autosave-v1/
---
I was given a new application to sequence:  
Logibec&#8217;s eClinibase.

This application has some restrictions:  
1) It has an updater that will pull down .exe&#8217;s  
2) It won&#8217;t launch from a script in the bubble. Â Example:  
cmd.exe /appvve:%PACKAGEGUID%_%VERSIONGUID%  
cd /d C:LogibeceClinibaseilgi11  
eclinibase.exe

Will crash.

I&#8217;m going to address point 2) first because it&#8217;s weird and interesting.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/1-7.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/1-7-300x118.png" width="320" height="125" border="0" /></a>
</div>

Why does it crash? Â Because, somehow, it breaks out of the bubble. Â The reason it crashes is it looking for some registry keys that don&#8217;t exist outside the bubble and it explodes. Â The keys it looks for are:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/3-5.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/3-5-300x64.png" width="320" height="67" border="0" /></a>
</div>

On a local install if I delete the environment keys that matchup to the folder we&#8217;re launching eClinibase from, I get the same error. Â Since those keys don&#8217;t exist outside the bubble, we get the same error&#8230; Â Which means it&#8217;s trying to look outside the bubble for some reason. Â Powershell, however, works correctly but only if the exe is called directly:

<pre class="lang:batch decode:true ">powershell.exe  -executionPolicy byPass -command "&{Start-AppvVirtualProcess -AppvClientObject(Get-AppvClientPackage Logibec_eClinibase_910.0.3.3_x86) C:\LOGIBEC\eClinibase\ilgi11\eClinibase.exe}"</pre>

However, we CAN get it to stay &#8216;in the bubble&#8217; if we launch the eClinibase.exe with the /appvve: switch.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://theorypc.ca/wp-content/uploads/2016/03/4-3.png"><img src="http://theorypc.ca/wp-content/uploads/2016/03/4-3-300x117.png" width="320" height="124" border="0" /></a>
</div>

This is the first time I&#8217;ve ever encountered this where launching the application from a command prompt inside the AppV bubble fails, but it will work if you launch the exe with the /appvve: switch.

So that&#8217;s weird that we need to launch it specifically that way, but it appears to work so I guess that&#8217;s a limitation we have to roll with.

So how do we address point 1)?

I&#8217;ve used this technique before and it works here. Â We will create a directory symlink to a network file share where the users will have full permission to update the application. Â Since these updates occur &#8216;as needed&#8217; the different environments may update at different times, this keeps them in sync across all of our Citrix servers since they will all point to the same location.

I add the mklink command the DynamicDeploymentConfig.xml:  
And we are good to go!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->