---
id: 2824
title: 'User Profile Manager &#8211; Unavoidable Delays'
date: 2018-06-30T21:40:22-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2018/06/30/2807-autosave-v1/
permalink: /2018/06/30/2807-autosave-v1/
---
<img class="aligncenter size-full wp-image-2821" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM.png" alt="" width="454" height="48" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM.png 454w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.45.09-PM-300x32.png 300w" sizes="(max-width: 454px) 100vw, 454px" />

https://twitter.com/TrententTye/status/1011396648688185344

I&#8217;ve been exploring optimizing logon times and noticed &#8220;User Profile Service&#8221; always showed up for 1-3 seconds. Â I asked why and began my investigation.

The first thing I needed to do is separate the &#8220;User Profile Service&#8221; into it&#8217;s own process. Â It&#8217;s originally configured to share the same process as other services which makes procmon&#8217;ing difficult.

<img class="aligncenter size-full wp-image-2808" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM.png" alt="" width="644" height="365" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM.png 644w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.44.11-PM-300x170.png 300w" sizes="(max-width: 644px) 100vw, 644px" /> 

Making this change is easy:

<img class="aligncenter size-full wp-image-2809" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM.png" alt="" width="484" height="211" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM.png 484w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.45.36-PM-300x131.png 300w" sizes="(max-width: 484px) 100vw, 484px" /> 

<img class="aligncenter size-large wp-image-2810" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM.png" alt="" width="662" height="478" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM.png 662w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-1.46.17-PM-300x217.png 300w" sizes="(max-width: 662px) 100vw, 662px" /> 

Now that the User Profile Service is running in it&#8217;s own process we can use Process Monitor to target that PID.

I logged onto the RDS server with a different account and started my procmon trace. Â I then logged into server:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2824-113" width="1140" height="510" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2018/06/UserProfileService_Delay.mp4?_=113" /><a href="http://theorypc.ca/wp-content/uploads/2018/06/UserProfileService_Delay.mp4">http://theorypc.ca/wp-content/uploads/2018/06/UserProfileService_Delay.mp4</a></video>
</div>

One of the beautiful things about a video like this is we can start to go through frame-by-frame if needed to observe the exact events that are occurring. Â Process Monitor also gives us a good overview of what&#8217;s happening with the &#8220;Process Activity&#8221; view:

<img class="aligncenter size-full wp-image-2814" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM.png" alt="" width="1222" height="45" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM.png 1222w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM-300x11.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.30.42-PM-768x28.png 768w" sizes="(max-width: 1222px) 100vw, 1222px" /> 

9,445 file events, 299,668 registry events. Â Registry, by far, has the most events occurring on it. Â And so we investigate:

  1. On new logins the registry hive is copied from Default User to your profile directory, the hive is mounted and than security permissions are set.  
<img class="aligncenter size-full wp-image-2813" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM.png" alt="" width="1276" height="538" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM.png 1276w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM-300x126.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.26.50-PM-768x324.png 768w" sizes="(max-width: 1276px) 100vw, 1276px" /><img class="aligncenter size-full wp-image-2815" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM.png" alt="" width="1233" height="195" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM.png 1233w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM-300x47.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.35.25-PM-768x121.png 768w" sizes="(max-width: 1233px) 100vw, 1233px" />  
    Setting the initial permissions of the user hive began at 2:14:46.3208182 and finished at 2:14:46.4414112. Â Spanning a total time of <span style="text-decoration: underline;"><strong>121</strong></span> milliseconds. Â Pretty quick but to minimize logon duration it&#8217;s worth examining each key in the Default User hive and ensuring you do not have any unnecessary keys. Â Each of these keys will have their permissions evaluated and modified.
  2. The Profile Notification system now kicks off.  
<img class="aligncenter size-full wp-image-2816" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM.png" alt="" width="1264" height="832" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM.png 1264w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM-300x197.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.39.10-PM-768x506.png 768w" sizes="(max-width: 1264px) 100vw, 1264px" />  
    The user profile server now goes through each &#8220;ProfileNotification&#8221; and, if it&#8217;s applicable, executes whatever action the module is responsible for. Â In my screenshot we can see the User Profile Service alerts the &#8220;WSE&#8221;. Â Each key actually contains the friendly name, giving you a hint about its role:  
<img class="aligncenter size-full wp-image-2817" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM.png" alt="" width="937" height="183" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM.png 937w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM-300x59.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.45.44-PM-768x150.png 768w" sizes="(max-width: 937px) 100vw, 937px" />  
    It also appears we can measure the duration of each module by the &#8220;RegOpenKey&#8221; and &#8220;RegCloseKey&#8221; events tied to that module.  
<img class="aligncenter size-full wp-image-2818" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM.png" alt="" width="877" height="751" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM.png 877w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM-300x257.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-2.48.23-PM-768x658.png 768w" sizes="(max-width: 877px) 100vw, 877px" />  
    In my procmon log, the WSE took 512ms, the next module &#8220;WinBio&#8221; took 1ms, etc. Â The big time munchers for my system were:  
    WSE: 512ms  
    SyncCenter: 260ms  
    SHACCT: 14ms  
    SettingProfileHandler: 4ms  
    GPSvc: 59ms  
    GamesUX: 60ms  
    DefaultAssociationsProfileHandler:Â 4450ms (!)<img class="aligncenter size-full wp-image-2819" src="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM.png" alt="" width="1230" height="875" srcset="http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM.png 1230w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM-300x213.png 300w, http://theorypc.ca/wp-content/uploads/2018/06/Screen-Shot-2018-06-30-at-3.06.45-PM-768x546.png 768w" sizes="(max-width: 1230px) 100vw, 1230px" />
  3. In the previous screenshot we can see the ProfileNotification has two events kicked off that it runs through it&#8217;s list of modules: Create and Load. Â Load takes 153ms in total, so Create is what is triggering our event.
  4. DefaultAssociationsProfileHandler consumes the majority of the User Profile Service time. Â What the heck is it doing? Â It appears the Default Association Profile Handler is responsible for creating the associations between several different components and your ability to customize them. Â It associates (that I can see):  
    ApplicationToasts (eg, popup notifications)  
    RegisteredApplications  
    File Extensions  
    DefaultPrograms  
    UrlAssociations  
    The GPO &#8220;Set Default Associations via XML file&#8221; is processed and the above is re-run with the XML file values.
  5. Do we need these associations? 
    Honestly&#8230; Â Maybe.
    
    However, does this need to be \*blocking\* the login process? Â Probably not. Â This could be an option to be run asynchronously with you, as the admin, gambling that any required associations will be set before the user gets the desktop/app&#8230; Â Or if you have applications that are entirely single purpose that simply read and write to a database somewhere than this is superfluous.</li> 
    
      * Can we disable it? 
        Yes&#8230;
        
        But I&#8217;m on the fence if this is a good idea. Â To disable it, I&#8217;ve found deleting the &#8220;DefaultAssociationsProfileHandler&#8221; does work, associations are skipped and we logon 1-4 seconds faster. Â However, launching a file directly or shortcut with a url handler will prompt you to choose your default program (as should be expected).</li> </ol> 
        
        https://twitter.com/selgan/status/1011443091503505408
        
        I&#8217;m exploring this idea. Â Deleting the key entirely and using SetUserFTA to set associations.
        
        We have ~400 App-V applications that write/overwrite approximately 800 different registered applications and file extensions into our registry hive (we publish globally &#8212; this puts them there). Â This is the reason why I started this investigation is some of our servers with lots of AppV applications were reporting longer UserProfileService times and tying it all together, this one module in the User Profile Service appears to be the culprit. Â And with Spectre increasing the duration of registry operations 400% this became noticeable real quick in our testing.
        
        Lastly, time isÂ _**still**__Â_ being consumed on RDS and server platforms by the infuriatingÂ <span style="text-decoration: underline;"><strong>garbage</strong></span>Â services like GamesUX (&#8220;Games Explorer&#8221;). Â It just tweaks a nerve a little bit when I see time being consumed on wasteful processes.
        
        <!-- AddThis Advanced Settings generic via filter on the_content -->
        
        <!-- AddThis Share Buttons generic via filter on the_content -->