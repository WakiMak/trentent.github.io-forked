---
id: 2807
title: User Profile Manager - Unavoidable Delays
date: 2018-06-30T15:47:22-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2807
permalink: /2018/06/30/user-profile-manager-unavoidable-delays/
enclosure:
  - |
    /wp-content/uploads/2018/06/UserProfileService_Delay.mp4
    198
    video/mp4
    
image: /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.27-PM.png
categories:
  - Blog
tags:
  - AppV
  - Citrix
  - Performance
  - procmon
  - Registry

---
<img class="aligncenter size-full wp-image-2821" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM.png" alt="" width="454" height="48" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM.png 454w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM-300x32.png 300w" sizes="(max-width: 454px) 100vw, 454px" />

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Found something annoying. Fresh 2016 install, all new users logins have the User Profile Service take a *minimum* of 1 second.<a href="https://twitter.com/hashtag/procmon?src=hash&amp;ref_src=twsrc%5Etfw">#procmon</a> it and find Default File Associations is the cause. This is done synchronously! The more file ext’s the slower the login!</p>&mdash; Trentent Tye (@TrententTye) <a href="https://twitter.com/TrententTye/status/1011396648688185344?ref_src=twsrc%5Etfw">June 25, 2018</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I've been exploring optimizing logon times and noticed "User Profile Service" always showed up for 1-3 seconds.  I asked why and began my investigation.

The first thing I needed to do is separate the "User Profile Service" into it's own process.  It's originally configured to share the same process as other services which makes procmon'ing difficult.

<img class="aligncenter size-full wp-image-2808" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM.png" alt="" width="644" height="365" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM.png 644w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM-300x170.png 300w" sizes="(max-width: 644px) 100vw, 644px" /> 

Making this change is easy:

<img class="aligncenter size-full wp-image-2809" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM.png" alt="" width="484" height="211" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM.png 484w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM-300x131.png 300w" sizes="(max-width: 484px) 100vw, 484px" /> 

<img class="aligncenter size-large wp-image-2810" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM.png" alt="" width="662" height="478" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM.png 662w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM-300x217.png 300w" sizes="(max-width: 662px) 100vw, 662px" /> 

Now that the User Profile Service is running in it's own process we can use Process Monitor to target that PID.

I logged onto the RDS server with a different account and started my procmon trace.  I then logged into server:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2807-34" width="1140" height="510" preload="metadata" controls="controls"><source type="video/mp4" src="/wp-content/uploads/2018/06/UserProfileService_Delay.mp4?_=34" /><a href="/wp-content/uploads/2018/06/UserProfileService_Delay.mp4">/wp-content/uploads/2018/06/UserProfileService_Delay.mp4</a></video>
</div>

One of the beautiful things about a video like this is we can start to go through frame-by-frame if needed to observe the exact events that are occurring.  Process Monitor also gives us a good overview of what's happening with the "Process Activity" view:

<img class="aligncenter size-full wp-image-2814" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM.png" alt="" width="1222" height="45" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM.png 1222w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM-300x11.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM-768x28.png 768w" sizes="(max-width: 1222px) 100vw, 1222px" /> 

9,445 file events, 299,668 registry events.  Registry, by far, has the most events occurring on it.  And so we investigate:

  1. On new logins the registry hive is copied from Default User to your profile directory, the hive is mounted and than security permissions are set.  
<img class="aligncenter size-full wp-image-2813" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM.png" alt="" width="1276" height="538" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM.png 1276w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM-300x126.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM-768x324.png 768w" sizes="(max-width: 1276px) 100vw, 1276px" /><img class="aligncenter size-full wp-image-2815" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM.png" alt="" width="1233" height="195" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM.png 1233w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM-300x47.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM-768x121.png 768w" sizes="(max-width: 1233px) 100vw, 1233px" />  
    Setting the initial permissions of the user hive began at 2:14:46.3208182 and finished at 2:14:46.4414112.  Spanning a total time of <span style="text-decoration: underline;"><strong>121</strong></span> milliseconds.  Pretty quick but to minimize logon duration it's worth examining each key in the Default User hive and ensuring you do not have any unnecessary keys.  Each of these keys will have their permissions evaluated and modified.
  2. The Profile Notification system now kicks off.  
<img class="aligncenter size-full wp-image-2816" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM.png" alt="" width="1264" height="832" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM.png 1264w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM-300x197.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM-768x506.png 768w" sizes="(max-width: 1264px) 100vw, 1264px" />  
    The user profile server now goes through each "ProfileNotification" and, if it's applicable, executes whatever action the module is responsible for.  In my screenshot we can see the User Profile Service alerts the "WSE".  Each key actually contains the friendly name, giving you a hint about its role:  
<img class="aligncenter size-full wp-image-2817" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM.png" alt="" width="937" height="183" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM.png 937w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM-300x59.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM-768x150.png 768w" sizes="(max-width: 937px) 100vw, 937px" />  
    It also appears we can measure the duration of each module by the "RegOpenKey" and "RegCloseKey" events tied to that module.  
<img class="aligncenter size-full wp-image-2818" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM.png" alt="" width="877" height="751" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM.png 877w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM-300x257.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM-768x658.png 768w" sizes="(max-width: 877px) 100vw, 877px" />  
    In my procmon log, the WSE took 512ms, the next module "WinBio" took 1ms, etc.  The big time munchers for my system were:  
    WSE: 512ms  
    SyncCenter: 260ms  
    SHACCT: 14ms  
    SettingProfileHandler: 4ms  
    GPSvc: 59ms  
    GamesUX: 60ms  
    DefaultAssociationsProfileHandler: 4450ms (!)<img class="aligncenter size-full wp-image-2819" src="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM.png" alt="" width="1230" height="875" srcset="/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM.png 1230w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM-300x213.png 300w, /wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM-768x546.png 768w" sizes="(max-width: 1230px) 100vw, 1230px" />
  3. In the previous screenshot we can see the ProfileNotification has two events kicked off that it runs through it's list of modules: Create and Load.  Load takes 153ms in total, so Create is what is triggering our event.
  4. DefaultAssociationsProfileHandler consumes the majority of the User Profile Service time.  What the heck is it doing?  It appears the Default Association Profile Handler is responsible for creating the associations between several different components and your ability to customize them.  It associates (that I can see):  
    ApplicationToasts (eg, popup notifications)  
    RegisteredApplications  
    File Extensions  
    DefaultPrograms  
    UrlAssociations  
    The GPO "Set Default Associations via XML file" is processed and the above is re-run with the XML file values.
  5. Do we need these associations? 
    Honestly...  Maybe.
    
    However, does this need to be \*blocking\* the login process?  Probably not.  This could be an option to be run asynchronously with you, as the admin, gambling that any required associations will be set before the user gets the desktop/app...  Or if you have applications that are entirely single purpose that simply read and write to a database somewhere than this is superfluous.</li> 
    
      * Can we disable it? 
        Yes...
        
        But I'm on the fence if this is a good idea.  To disable it, I've found deleting the "DefaultAssociationsProfileHandler" does work, associations are skipped and we logon 1-4 seconds faster.  However, launching a file directly or shortcut with a url handler will prompt you to choose your default program (as should be expected).</li> </ol> 
        
        https://twitter.com/selgan/status/1011443091503505408
        
        I'm exploring this idea.  Deleting the key entirely and using SetUserFTA to set associations.
        
        We have ~400 App-V applications that write/overwrite approximately 800 different registered applications and file extensions into our registry hive (we publish globally - this puts them there).  This is the reason why I started this investigation is some of our servers with lots of AppV applications were reporting longer UserProfileService times and tying it all together, this one module in the User Profile Service appears to be the culprit.  And with Spectre increasing the duration of registry operations 400% this became noticeable real quick in our testing.
        
        Lastly, time is _**still**___ being consumed on RDS and server platforms by the infuriating <span style="text-decoration: underline;"><strong>garbage</strong></span> services like GamesUX ("Games Explorer").  It just tweaks a nerve a little bit when I see time being consumed on wasteful processes.
        
        <!-- AddThis Advanced Settings generic via filter on the_content -->
        
        <!-- AddThis Share Buttons generic via filter on the_content -->
