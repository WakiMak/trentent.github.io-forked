---
id: 1427
title: Windows Server 2012 R2 cache drive size for parity drives
date: 2016-04-28T23:37:27-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/630-revision-v1/
permalink: /2016/04/28/630-revision-v1/
---
It turns out the maximum a parity drive write cache size can be is 100GB. &nbsp;I have a 500GB SSD (~480GB real capacity) so the maximum write cache size I can make for a volume is 100GB. &nbsp;I suspect I maybe able to create multiple volumes and have each of them with a write cache of 100GB. &nbsp;Until then this is the biggest it seems you can make for a single volume, so MS solves that issue of having a too large write cache.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->