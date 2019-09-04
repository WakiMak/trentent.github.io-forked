---
id: 630
title: Windows Server 2012 R2 cache drive size for parity drives
date: 2013-08-19T10:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/08/19/windows-server-2012-r2-cache-drive-size-for-parity-drives/
permalink: /2013/08/19/windows-server-2012-r2-cache-drive-size-for-parity-drives/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/08/windows-server-2012-r2-cache-drive-size.html
blogger_internal:
  - /feeds/7977773/posts/default/8892420017853202491
categories:
  - Blog
  - Uncategorized
tags:
  - 2012R2
---
It turns out the maximum a parity drive write cache size can be is 100GB. &nbsp;I have a 500GB SSD (~480GB real capacity) so the maximum write cache size I can make for a volume is 100GB. &nbsp;I suspect I maybe able to create multiple volumes and have each of them with a write cache of 100GB. &nbsp;Until then this is the biggest it seems you can make for a single volume, so MS solves that issue of having a too large write cache.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->