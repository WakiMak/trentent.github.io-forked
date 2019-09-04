---
id: 593
title: 'AppV5 &#8211; Update files in an already mounted package'
date: 2014-09-04T13:09:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/09/04/appv5-update-files-in-an-already-mounted-package/
permalink: /2014/09/04/appv5-update-files-in-an-already-mounted-package/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/09/appv5-update-files-in-already-mounted.html
blogger_internal:
  - /feeds/7977773/posts/default/2023260342091320900
categories:
  - Blog
tags:
  - AppV
---
An issue I&#8217;ve been dealing with lately is we have some in-house applications baked into an AppV package. &nbsp;We are encountering some issues with them and they require updating almost on a daily basis. &nbsp;Since you can&#8217;t update/copy .exe&#8217;s via a preconfig script or by breaking into the environment (CoW restrictions) they need to be baked into the environment.

One of the new features of AppV 5 vs. 4.6 is that it can mount the files as actual files in the filesystem as opposed to a single binary. &nbsp;This exposure of the AppV 5 packages allows for manipulation of the files \*after\* they are deployed, but you need to make some modifications to the mounted files.



<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-qmF1rBFSU2E/VAi0kSZFTZI/AAAAAAAAAiE/_0X3xQUk8eM/s1600/1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-qmF1rBFSU2E/VAi0kSZFTZI/AAAAAAAAAiE/_0X3xQUk8eM/s1600/1.png" height="53" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

1) Navigate to the folder you want to change, right click on it and take ownership of it. &nbsp;Make sure you replace permissions on all items within the folder.

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-mVtUHqIXWY8/VAi0kWGgIxI/AAAAAAAAAiQ/G0-xEjaAF3I/s1600/2.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-mVtUHqIXWY8/VAi0kWGgIxI/AAAAAAAAAiQ/G0-xEjaAF3I/s1600/2.png" height="251" width="320" /></a>
</div>

<div>
  <div>
  </div>
  
  <div>
    2) Set the permissions on the folder to Everyone Full.
  </div>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-jUb5rnNCza4/VAi0kfu276I/AAAAAAAAAiI/qilgIwNEkhc/s1600/3.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-jUb5rnNCza4/VAi0kfu276I/AAAAAAAAAiI/qilgIwNEkhc/s1600/3.png" height="234" width="320" /></a>
</div>

<div>
</div>

<div>
  And at this part you can now replace .exe&#8217;s within that folder and subsequent launches of the application now use the new .exe&#8217;s. &nbsp;At this stage I can now replace these files without recreating the package and when another update to that .exe comes down the pipe I don&#8217;t have to open the sequencer to update a file, update the share, update the batch file with the new /appvve: GUID&#8217;s. &nbsp;At least until their software becomes stable and final for this build.
</div>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->