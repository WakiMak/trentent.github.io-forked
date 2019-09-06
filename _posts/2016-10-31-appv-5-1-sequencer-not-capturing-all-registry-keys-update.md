---
id: 1797
title: AppV 5.1 Sequencer â€“ Not capturing all registry keys - Update
date: 2016-10-31T15:31:05-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1797
permalink: /2016/10/31/appv-5-1-sequencer-not-capturing-all-registry-keys-update/
image: /wp-content/uploads/2016/10/Capture.png
categories:
  - Blog
tags:
  - AppV
  - Performance
  - Registry
---
[My previous post touched on an issue we are having with some applications](https://theorypc.ca/2016/09/20/appv-5-1-sequencer-not-capturing-all-registry-keys/). Â The AppV sequencer is not capturing all registry keys. Â I have been working with Microsoft on this issue for over 2 years but just recently got some headway with getting this addressed. Â And I have good news and bad news. Â The good news is the root cause for this issue appears to have been found.

It appears that ETW (Event Tracing for Windows) will capture some of the events out of order and the AppV sequencer will then apply that out of order sequence. Â The correct sequence of events should be:  
RegDeleteKey  
RegCreateKey

But in certain scenarios it's capturing the events as:

RegCreateKey  
RegDeleteKey

By capturing the deletion last in the order, the AppV sequencer is effectively being told to 'Not' capture the registry key.

Microsoft has corrected this issue in the 'Anniversary' edition of Windows 10 (Build 14393+) and sequencing in this OS will capture all the registry keys correctly.

The bad news is Microsoft is evaluating backporting the fix to older versions of Windows. Â Specifically Windows 2008 R2. Â Windows 2008R2 is still widely used and AppV best practice is to sequence on the OS you plan on deploying but if the older OS sequences unreliably this complicates the ability to 'trust' the product. Â This fix still needs to be backported to 2012, 2012R2 and the related client OS's, so hopefully they get it as well. Â The reason I was told 2008 R2 may not get the fix is that it is no longer in Mainstream support, but Windows 7SP1 currently is, which is analogous to 2008 R2. Â So hopefully the effort to fix this bug will be backported and we'll have a solid AppV platform where we can sequence our packages in confidence.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->