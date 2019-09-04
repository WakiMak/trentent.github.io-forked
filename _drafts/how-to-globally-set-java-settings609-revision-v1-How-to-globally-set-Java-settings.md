---
id: 1063
title: How to globally set Java settings
date: 2016-04-24T22:44:17-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/609-revision-v1/
permalink: /2016/04/24/609-revision-v1/
---
How to set Java settings on a machine/system/global level:

1) Create a new text file atÂ &#8220;C:\Windows\Sun\Java\Deployment\deployment.config&#8221;  
2) populate it with two values:

<pre class="lang:default decode:true ">#deployment.config
#May 15 2014
# The First line below specifies if this config is mandatory which is simple enough
# The second line just tells Java where to the properties of your Java Configuration
# NOTE: These java settings will be applied to each user file and will overwrite existing ones
deployment.system.config.mandatory=True
deployment.system.config=file\:C\:/WINDOWS/Sun/Java/Deployment/deployment.properties</pre>

Create or copy deployment.properties at  
(You can copy the one created for your user profile @ %userprofile%\LocalLow\Sun\Java\Deployment\deployment.properties<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Â </span>as a template).

Modify the values as needed (example):

<pre class="lang:default decode:true ">#deployment.properties
deployment.security.level=MEDIUM</pre>

<div>
</div>

If the user has an existing deployment.properties under their user profile you may need to delete it. Â On next java launch it will create a deployment.properties under their profile with the values you specified in the global file missing from their local file.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->