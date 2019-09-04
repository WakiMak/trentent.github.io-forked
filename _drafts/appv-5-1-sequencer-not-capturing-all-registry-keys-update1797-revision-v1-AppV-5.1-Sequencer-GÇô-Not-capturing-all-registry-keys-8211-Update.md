---
id: 1800
title: 'AppV 5.1 Sequencer â€“ Not capturing all registry keys &#8211; Update'
date: 2016-10-31T15:35:24-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/10/31/1797-revision-v1/
permalink: /2016/10/31/1797-revision-v1/
---
[My previous post touched on an issue we are having with some applications](https://theorypc.ca/2016/09/20/appv-5-1-sequencer-not-capturing-all-registry-keys/). Â The AppV sequencer is not capturing all registry keys. Â I have been working with Microsoft on this issue for over 2 years but just recently got some headway with getting this addressed. Â And I have good news and bad news. Â The good news is the root cause for this issue appears to have been found.

It appears that ETW (Event Tracing for Windows) will capture some of the events out of order and the AppV sequencer will then apply that out of order sequence. Â The correct sequence of events should be:  
RegDeleteKey  
RegCreateKey

But in certain scenarios it&#8217;s capturing the events as:

RegCreateKey  
RegDeleteKey

By capturing the deletion last in the order, the AppV sequencer is effectively being told to &#8216;Not&#8217; capture the registry key.

Microsoft has corrected this issue in the &#8216;Anniversary&#8217; edition of Windows 10 (Build 14393+) and sequencing in this OS will capture all the registry keys correctly.

The bad news is Microsoft is evaluating backporting the fix to older versions of Windows. Â Specifically Windows 2008 R2. Â Windows 2008R2 is still widely used and AppV best practice is to sequence on the OS you plan on deploying but if the older OS sequences unreliably this complicates the ability to &#8216;trust&#8217; the product. Â This fix still needs to be backported to 2012, 2012R2 and the related client OS&#8217;s, so hopefully they get it as well. Â The reason I was told 2008 R2 may not get the fix is that it is no longer in Mainstream support, but Windows 7SP1 currently is, which is analogous to 2008 R2. Â So hopefully the effort to fix this bug will be backported and we&#8217;ll have a solid AppV platform where we can sequence our packages in confidence.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->