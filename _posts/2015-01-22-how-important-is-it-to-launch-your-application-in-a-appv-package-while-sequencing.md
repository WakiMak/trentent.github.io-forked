---
id: 579
title: How important is it to launch your application in a AppV package while sequencing?
date: 2015-01-22T11:51:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/01/22/how-important-is-it-to-launch-your-application-in-a-appv-package-while-sequencing/
permalink: /2015/01/22/how-important-is-it-to-launch-your-application-in-a-appv-package-while-sequencing/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/01/how-important-is-it-to-launch-your.html
blogger_internal:
  - /feeds/7977773/posts/default/1608616607591745379
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
For a bit I was of the mindset that sequencing a AppV package should be as clean as humanely possible. Â This would include finding all configuration tweaks to files/registry keys ahead of time and implementing them so any registry keys generated would be unique to the user. Â I've seen some applications generate a unique GUID that vendor's would use to lock it so that the application was tied to one machine. Â Since this key would be generated in HKLM so all users would be able to see the key, it prevented new launches.

But if you didn't launch the application while sequencing, the key wouldn't get generated until the user launched it in their bubble. Â This effectively allowed multiple users to use the same application on one server. Â With this new information in mind, and a new outlook on keeping the AppV registry hive to a bare minimum; forward I strode. Â And hard into a wall I ran.

What I eventually ended up finding was applications that error'ed on first launch:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-lEtGeCiwc4E/VMEySl2-jFI/AAAAAAAAAtI/ikBp2FWTcGQ/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.24.14%2BAM.png"><img src="http://3.bp.blogspot.com/-lEtGeCiwc4E/VMEySl2-jFI/AAAAAAAAAtI/ikBp2FWTcGQ/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.24.14%2BAM.png" width="320" height="183" border="0" /></a>
</div>

But would launch just fine the second time:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-dk8d7kQpbzg/VMEyt4YdpyI/AAAAAAAAAtQ/opYs8D-K7OE/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.26.06%2BAM.png"><img src="http://3.bp.blogspot.com/-dk8d7kQpbzg/VMEyt4YdpyI/AAAAAAAAAtQ/opYs8D-K7OE/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.26.06%2BAM.png" width="320" height="221" border="0" /></a>
</div>

So what would cause it to fail the first time? Â I imagine for most cases the error message is caused by missing file(s) or registry key(s) or values. Â So how do you find these newly generated profile? Â Well, the nice thing about AppV is it stores all these things in two places. Â Your registry hive or your user profile.

Before launching the application I did a "dir /s /b C:UsersAdtest91 >>Clean.txt" and saved that to a text file. Â I then launched the program twice and ran this command "dir /s /b C:UsersAdtest91 >> Working.txt" Â I then compared the files and found the following new paths generated:

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">C:\Users\adtest91\AppData\Local\Microsoft\AppVClient\VFS\ADB25534-3FE9-44BD-9FC8-D5AAD8C0E728</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">C:\Users\adtest91\DesktopFax</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;"><br /> </span>To see if these two paths caused my issue I deleted them and relaunched the application. Â The application launched just fine. Â I then backed up those two folders. Â To rule out the file system with some more finality, I deleted my user profile then copied my two backup folders to their recorded paths and tried launching the program and got the error message again. Â With that I felt confident I could rule out files/folders as the cause.

The beautiful thing about AppV registry changes is they are recorded to your user profile. Â This is stored here:  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">HKEY_USERS\!GUID!_Classes\AppV\Client\Packages</span>

Export this key prior to launching your application, launch your application, and export the key again and do a difference. Â Any created or modified registry keys will reside in this location for you to examine.

For this issue, this was the key that was generated:

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">[HKEY_USERS\S-1-5-21-38857442-2693285798-3636612711-15053136_Classes\AppV\Client\Packages\ADB25534-3FE9-44BD-9FC8-D5AAD8C0E728\REGISTRY\MACHINE\SOFTWARE\Classes\TypeLib\{3B7C8863-D78F-101B-B9B5-04021C009402}\1.2\0\win32]</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">@="C:\\Windows\\system32\\Richtx32.ocx"</span>

Deciphering the key results in the following missing value:  
<span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">[HKLM\SOFTWARE\Classes\TypeLib\{3B7C8863-D78F-101B-B9B5-04021C009402}\1.2\0\win32]</span>

Looking locally on the server, this is what I saw in the registry:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-VB5MMpj9m7U/VME3XmL-1lI/AAAAAAAAAtY/Jqn9OZkr5Fk/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.45.51%2BAM.png"><img src="http://1.bp.blogspot.com/-VB5MMpj9m7U/VME3XmL-1lI/AAAAAAAAAtY/Jqn9OZkr5Fk/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.45.51%2BAM.png" width="320" height="95" border="0" /></a>
</div>

There was nothing in the Data field! Â AppV5 'integrates' the Classes key in your AppV package when you publish it. Â I resequenced and launched the application after the install and checked the key again:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-ckbMtSA00yo/VME4LDFsK9I/AAAAAAAAAtg/l2_6xP_trH8/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.49.17%2BAM.png"><img src="http://4.bp.blogspot.com/-ckbMtSA00yo/VME4LDFsK9I/AAAAAAAAAtg/l2_6xP_trH8/s1600/Screen%2BShot%2B2015-01-22%2Bat%2B10.49.17%2BAM.png" width="320" height="42" border="0" /></a>
</div>

Surprise, surprise. Â And now the application launches without issue. Â So it appears that some application installers don't completely register all files (OCX files are two instances of this issue happening that I noticed) until the application is launched. Â So now, our policy will always to, at a minimum, launch the application while sequencing.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->