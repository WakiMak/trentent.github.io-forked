---
id: 711
title: Maya and rendering priorities
date: 2011-04-19T09:36:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/04/19/maya-and-rendering-priorities/
permalink: /2011/04/19/maya-and-rendering-priorities/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/04/maya-and-rendering-priorities.html
blogger_internal:
  - /feeds/7977773/posts/default/3319018093427972581
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
I have a small farm of computers at home that I want to use as a render farm. Some of them are used by my family though and starting Maya renderings on them greatly diminishes their usability during that time. What needs to be done is force the priority lower on the rendering app (mayabatch.exe). 3D Studio Max has a script called "serverpriority.ms" that does this and it's essentially one line that, upon startup, sets the priority to low. I've been unable to find one like that for Maya but have found an acceptable workaround.

<div>
</div>

<div>
  If you start the Backburner server.exe app as low priority all threads that the apps server.exe starts (like cmdjob and mayabatch.exe) will start in the same priority. Start the server.exe as low and your rendering threads will be low. To start server.exe in a low priority mode via command-line it looks like this:
</div>

<div>
</div>

<div>
  <pre class="lang:default decode:true ">START "" /LOW "pathtoserver.exe"</pre>
</div>

<div>
</div>

<div>
  Done!
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->