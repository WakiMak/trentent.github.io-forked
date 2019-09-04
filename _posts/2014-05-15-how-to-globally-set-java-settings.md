---
id: 609
title: How to globally set Java settings
date: 2014-05-15T10:06:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/05/15/how-to-globally-set-java-settings/
permalink: /2014/05/15/how-to-globally-set-java-settings/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/05/how-to-globally-set-java-settings.html
blogger_internal:
  - /feeds/7977773/posts/default/959475313306239234
categories:
  - Blog
  - Uncategorized
tags:
  - Java
---
How to set Java settings on a machine/system/global level:

1) Create a new text file atÂ "C:\Windows\Sun\Java\Deployment\deployment.config"  
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