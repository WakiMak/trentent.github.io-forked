---
id: 1596
title: 'Citrix & - Graphical Artifacts'
date: 2016-07-22T16:54:58-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1596
permalink: /2016/07/22/citrix-&-graphical-artifacts/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_happening.mp4
    193
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2016/07/20160722-160129.mp4
    189
    video/mp4
    
  - |
    http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_not_happening.mp4
    197
    video/mp4
    
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - Performance
  - procmon
  - Registry
  - &
---
In our Citrix & 6.5 environment we started having a couple applications encounter an issue where they would experience some serious graphical artifacts. Â What was supposed to look like this:

<img class="aligncenter size-full wp-image-1597" src="http://theorypc.ca/wp-content/uploads/2016/07/1-1.png" alt="1" width="1915" height="1122" srcset="http://theorypc.ca/wp-content/uploads/2016/07/1-1.png 1915w, http://theorypc.ca/wp-content/uploads/2016/07/1-1-300x176.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/1-1-768x450.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/1-1-1024x600.png 1024w" sizes="(max-width: 1915px) 100vw, 1915px" /> 

Would look like this:

<img class="aligncenter size-full wp-image-1598" src="http://theorypc.ca/wp-content/uploads/2016/07/2-1.png" alt="2" width="1916" height="1120" srcset="http://theorypc.ca/wp-content/uploads/2016/07/2-1.png 1916w, http://theorypc.ca/wp-content/uploads/2016/07/2-1-300x175.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/2-1-768x449.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/2-1-1024x599.png 1024w" sizes="(max-width: 1916px) 100vw, 1916px" /> 

&nbsp;

Here's a short video demonstrating this issue:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1596-6" width="1140" height="670" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_happening.mp4?_=6" /><a href="http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_happening.mp4">http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_happening.mp4</a></video>
</div>

Or sometimes it would show the windows \*behind\* the artifacted image. Â That is, instead of the 'White' you see in my image, the application behind it shows through.

When investigating this we found there was a couple symptoms that we were going to experience these artifacts.

  1. The window would become 'frosted' or 'ghosted' (as seen in Spy++ or AutoIt Window Info)  
<img class="aligncenter size-full wp-image-1600" src="http://theorypc.ca/wp-content/uploads/2016/07/4-1.png" alt="4" width="382" height="388" srcset="http://theorypc.ca/wp-content/uploads/2016/07/4-1.png 382w, http://theorypc.ca/wp-content/uploads/2016/07/4-1-295x300.png 295w, http://theorypc.ca/wp-content/uploads/2016/07/4-1-50x50.png 50w" sizes="(max-width: 382px) 100vw, 382px" /> 
  2. The application would switch to 'Not Responding'  
<img class="aligncenter size-full wp-image-1599" src="http://theorypc.ca/wp-content/uploads/2016/07/3.png" alt="3" width="427" height="137" srcset="http://theorypc.ca/wp-content/uploads/2016/07/3.png 427w, http://theorypc.ca/wp-content/uploads/2016/07/3-300x96.png 300w" sizes="(max-width: 427px) 100vw, 427px" /> 
  3. If you completed the task 'Edit' quickly there would be no artifacting (time was important)
  4. When 'timing' the switch from 'normal' to frosted or ghosted window it would be around 5-7 seconds. <div style="width: 612px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-1596-7" width="612" height="548" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/07/20160722-160129.mp4?_=7" /><a href="http://theorypc.ca/wp-content/uploads/2016/07/20160722-160129.mp4">http://theorypc.ca/wp-content/uploads/2016/07/20160722-160129.mp4</a></video>
    </div>

So what's going on here?

For this particular instance, the application is launching MSPaint with some modified properties. Â It sets Paint to 'Always On Top', which in itself isn't an issue, but then it **purposefully locks the UI** so you must complete the drawing and close paint before continuing. Â This is how the vendor designed this application to operate with this workflow.

And what's Windows doing? Â It turns out, Windows is trying to alert you that your program is non-responsive! Â [Windows has a built in feature to 'Frost/Ghost' the window of a non-responsive UI to prevent you from entering input that won't be received](https://blogs.msdn.microsoft.com/meason/2010/01/04/windows-error-reporting-for-hangs/). Â The ghosting effect is time sensitive! Â So that explains why if we opened and closed our document quickly their would be no artifacting but if we manipulated it for some time the artifacts would appear. Â The time limit for monitoring unresponsiveness is 5 seconds. Â DWM.exe is the process responsible for creating the 'Frost' window and when responsive returns, it appears it does a poor job telling the application to repaint all affected Windows.

[Microsoft recommends a couple 'fixes' which is really a programmatic way to 'disable' the ghosting feature](https://support.microsoft.com/en-us/kb/934228). Â The two methods Microsoft suggests is to create a NoGhost application compatibility fix or have the programmer use '<span class="text-base">DisableProcessWindowsGhosting'. Â But there is a 3rd method.</span>

The [5 second time limit is programmable](https://technet.microsoft.com/en-us/library/cc978614.aspx). Â If we extend the timeout we don't need to configure 'NoGhost' compatibility fixes for each app or go back to the vendor. Â The timer is global and affects every application and window. Â Unfortunately, I know of no way to permanently disable it, but we can set a high enough value to prevent it from appearing.

So what do we have to do to 'resolve' this?

**My preferred choice was to use Group Policy Preferences (Registry) and set a new value for HKCU\Control Panel\Desktop /v HungAppTimeout /t REG_SZ /d 120000**

This sets the timeout to 2 minutes as opposed to 5 seconds. Â Now when our program is used we get this result:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1596-8" width="1140" height="670" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_not_happening.mp4?_=8" /><a href="http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_not_happening.mp4">http://theorypc.ca/wp-content/uploads/2016/07/Artifacts_not_happening.mp4</a></video>
</div>

No More Artifacts.

When I was investigating this I found I could get the artifacting to occur in both ICA and RDP but not when on the console. Â The frustrating thing about this issue is that it was not consistent. Â Because of the 5 second default time limit, the program(s) we had that would 'artifact' would sometimes complete their UI locking job faster than 5 seconds, but sometimes not. Â This lead to reports of artifacting occurring more often 'during the peak work hours' when the application/server/user load was the most. Â This makes sense as the higher load undoubtedly lead to everything being slower, the database, server, etc. leading to the application waiting longer and thus exceeding the timeout. Â I did find through the course of troubleshooting this issue that it seemed to 'go away' when I was trying to replicate after hours, which is frustrating to only have a slice of time to try and resolve this during peak hours.

Fortunately, after implementing the HungAppTimeout registry key the artifacts for several application 'went away'.

Lastly, [contrary to this article](https://blogs.msdn.microsoft.com/meason/2010/02/04/hungapptimeout/)Â you do NOT need to restart for this value to take effect. Â WinLogon.exe reads the HungAppTimeout value and then configures DWM accordingly when your profile loads. Â So for this value to take effect you only need to log on with this value already residing in your user's registry hive.

<img class="aligncenter size-full wp-image-1604" src="http://theorypc.ca/wp-content/uploads/2016/07/5-1.png" alt="5" width="1151" height="171" srcset="http://theorypc.ca/wp-content/uploads/2016/07/5-1.png 1151w, http://theorypc.ca/wp-content/uploads/2016/07/5-1-300x45.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/5-1-768x114.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/5-1-1024x152.png 1024w" sizes="(max-width: 1151px) 100vw, 1151px" /> 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->