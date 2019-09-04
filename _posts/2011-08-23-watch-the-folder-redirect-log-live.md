---
id: 699
title: Watch the folder redirect log live
date: 2011-08-23T15:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/08/23/watch-the-folder-redirect-log-live/
permalink: /2011/08/23/watch-the-folder-redirect-log-live/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/08/watch-folder-redirect-log-live.html
blogger_internal:
  - /feeds/7977773/posts/default/5477404842188178010
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
If you've enabled the folder redirect log, you can watch it on a remote computer using the tail command and SED.exe.

Currently, the fdeploy.log (for XP anyways) stores the log as a binary file with a NULL character between each character. To clean up this output you can pipe tail.exe into sed and tell sed to delete the NULL characters...

[<img id="BLOGGER_PHOTO_ID_5644164972883700738" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 400px; height: 300px;" src="http://1.bp.blogspot.com/-GrSWWOhRrYA/TlQaI4ZvbAI/AAAAAAAAAHM/QqU6KQmEELY/s400/fdeploy-bad.GIF" alt="" border="0" />](http://1.bp.blogspot.com/-GrSWWOhRrYA/TlQaI4ZvbAI/AAAAAAAAAHM/QqU6KQmEELY/s1600/fdeploy-bad.GIF)

To:

[<img id="BLOGGER_PHOTO_ID_5644165089326456818" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 400px; height: 303px;" src="http://3.bp.blogspot.com/-89Zd35DWn_Y/TlQaPqL23_I/AAAAAAAAAHU/COKmtkFNNMg/s400/fdeploy-good.GIF" alt="" border="0" />](http://3.bp.blogspot.com/-89Zd35DWn_Y/TlQaPqL23_I/AAAAAAAAAHU/COKmtkFNNMg/s1600/fdeploy-good.GIF)

The command to watch it is now:

<pre class="lang:batch decode:true ">tail -f \\gkwngq1\c$\WINDOWS\Debug\UserMode\fdeploy.log | sed "s/\x00//g"</pre>

Cool.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->