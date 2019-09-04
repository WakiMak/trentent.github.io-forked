---
id: 614
title: Slow screen refresh with Citrix Receiver 4.0 or 4.1 with old Citrix Farms
date: 2014-04-21T14:24:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/04/21/slow-screen-refresh-with-citrix-receiver-4-0-or-4-1-with-old-citrix-farms/
permalink: /2014/04/21/slow-screen-refresh-with-citrix-receiver-4-0-or-4-1-with-old-citrix-farms/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/04/slow-screen-refresh-with-citrix.html
blogger_internal:
  - /feeds/7977773/posts/default/6281911172823913547
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Receiver
  - scripting
---
I was having an issue with very slow screen redraws with Citrix Receiver 4.1 with a MetaFrame 4.5 farm (although I've read online it's applicable to Presentation Server 4.0). Â The fix is to enable the following registry key:

<pre class="lang:default decode:true ">:Fix for slow graphics performance on Legacy Farm
IF '%PROCESSOR_ARCHITECTURE%'=='x86' (
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\ICA Client\Engine\Lockdown Profiles\All Regions\Lockdown\Virtual Channels\Seamless Windows" /v DeferredUpdateMode /d False /f
) ELSE (
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\ICA Client\Engine\Lockdown Profiles\All Regions\Lockdown\Virtual Channels\Seamless Windows" /v DeferredUpdateMode /d False /f
)
</pre>

<div>
</div>

<div>
  Adding that key resolved the issue and now Citrix Receiver 4.1 now updates correctly on the 4.5 farm.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->