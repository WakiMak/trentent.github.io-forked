---
id: 590
title: 'Quick Hit &#8211; ICA-TCP#0 and disconnected sessions'
date: 2014-09-26T12:49:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/09/26/quick-hit-ica-tcp0-and-disconnected-sessions/
permalink: /2014/09/26/quick-hit-ica-tcp0-and-disconnected-sessions/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/09/quick-hit-ica-tcp0-and-disconnected.html
blogger_internal:
  - /feeds/7977773/posts/default/87114396175137270
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - XenApp
---
<span style="font-family: 'Segoe UI'; font-size: 9pt;">If you were to login to a Citrix server with your ID and you get ICA-TCP#0 as your session name, then disconnected your session, went to another computer and logged in (in the Metavision environment you don&#8217;t assume the exisiting session &#8212; you get a new one) you will actually take that sessionName &#8220;ICA-TCP#0&#8221; as the disconnected session made it available. If you reconnect to your disconnected session it will update the SessionName variable to ICA-TCP#1. </span>

I bring this up because I was attempting to use this variable in the registry to determine a user&#8217;s process list but it fails because of the disconnected session maintaining that same value.

<table>
</table>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->