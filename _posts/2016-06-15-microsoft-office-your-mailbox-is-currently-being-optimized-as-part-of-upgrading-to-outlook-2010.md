---
id: 1498
title: 'Microsoft Office &#8211; Your mailbox is currently being optimized as part of upgrading to Outlook 2010'
date: 2016-06-15T13:01:52-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1498
permalink: /2016/06/15/microsoft-office-your-mailbox-is-currently-being-optimized-as-part-of-upgrading-to-outlook-2010/
categories:
  - Blog
tags:
  - Citrix
  - procmon
  - Registry
---
I was using our Office 2010 through Citrix and started noticing I was getting this message:

<img class="aligncenter size-full wp-image-1499" src="http://theorypc.ca/wp-content/uploads/2016/06/1.png" alt="1" width="531" height="581" srcset="http://theorypc.ca/wp-content/uploads/2016/06/1.png 531w, http://theorypc.ca/wp-content/uploads/2016/06/1-274x300.png 274w" sizes="(max-width: 531px) 100vw, 531px" /> 

&#8220;Your mailbox is currently being optimized as part of upgrading to Outlook 2010. Â This one time process may take over 15 minutes and performance may be affected while optimization is in progress.&#8221;

&#8220;Upgrade Outlook Connector&#8221;

&#8220;You must upgrade to the latest version of Outlook Hotmail Connector to continue using this e-mail account.&#8221;

In addition, all of the options under &#8216;Account Settings&#8217; do nothing. Â This is in addition to my Home/Inbox being empty and my calendar blank. Â Attempting to create a calendar event results in:  
<img class="aligncenter size-full wp-image-1500" src="http://theorypc.ca/wp-content/uploads/2016/06/2.png" alt="2" width="229" height="117" /> 

I then tried to open the &#8216;Mail&#8217; control panel and found it was missing. Â I manually opened it by launching it from:  
&#8220;C:\Program Files (x86)\Microsoft Office\Office14\MLCFG32.CPL&#8221;

<img class="aligncenter size-full wp-image-1501" src="http://theorypc.ca/wp-content/uploads/2016/06/3.png" alt="3" width="413" height="271" srcset="http://theorypc.ca/wp-content/uploads/2016/06/3.png 413w, http://theorypc.ca/wp-content/uploads/2016/06/3-300x197.png 300w" sizes="(max-width: 413px) 100vw, 413px" /> 

From here, clicking any of the buttons resulted in nothing happening. Â Email Accounts&#8230; Data Files&#8230; did nothing. Â Clicking &#8216;Show Profiles&#8217; did work, though. Â So what the heck is going on? Â I opened up Process Monitor to try and find out. Â I opened up the &#8216;Mail&#8217; control panel via the command line, since this is a &#8216;Rundll32.exe&#8217; I set the procmon filter to &#8216;Command Line&#8217; &#8216;contains&#8217; &#8216;MLCFG&#8217; and clicked one of the non-functioning buttons. Â My result:

<img class="aligncenter size-full wp-image-1502" src="http://theorypc.ca/wp-content/uploads/2016/06/4.png" alt="4" width="509" height="197" srcset="http://theorypc.ca/wp-content/uploads/2016/06/4.png 509w, http://theorypc.ca/wp-content/uploads/2016/06/4-300x116.png 300w" sizes="(max-width: 509px) 100vw, 509px" /><img class="aligncenter size-full wp-image-1503" src="http://theorypc.ca/wp-content/uploads/2016/06/5.png" alt="5" width="766" height="161" srcset="http://theorypc.ca/wp-content/uploads/2016/06/5.png 766w, http://theorypc.ca/wp-content/uploads/2016/06/5-300x63.png 300w" sizes="(max-width: 766px) 100vw, 766px" /> 

PATH NOT FOUND. Â Why isn&#8217;t the path found? Â I opened a command prompt and followed the path doing a dir /x to show shortnames:

<img class="aligncenter size-full wp-image-1504" src="http://theorypc.ca/wp-content/uploads/2016/06/6.png" alt="6" width="776" height="263" srcset="http://theorypc.ca/wp-content/uploads/2016/06/6.png 776w, http://theorypc.ca/wp-content/uploads/2016/06/6-300x102.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/6-768x260.png 768w" sizes="(max-width: 776px) 100vw, 776px" /> 

Well, there is the issue. Â It&#8217;s showing as &#8216;MICROS~4&#8217; when it&#8217;s searching for &#8216;MICROS~2&#8217;. Â Doing a repair on office will cause re-registration to occur and this does resolve the issue, but I wanted to change my shortname instead of doing a repair. Â Since these are PVS servers I created a new version, mounted the vDisk and changed the shortnames via fsutil:

<img class="aligncenter size-full wp-image-1505" src="http://theorypc.ca/wp-content/uploads/2016/06/7.png" alt="7" width="679" height="445" srcset="http://theorypc.ca/wp-content/uploads/2016/06/7.png 679w, http://theorypc.ca/wp-content/uploads/2016/06/7-300x197.png 300w" sizes="(max-width: 679px) 100vw, 679px" /> 

In the screenshot, you can clearly see &#8216;Microsoft Office&#8217; is now &#8216;MICROS~2&#8217;. Â I unmounted the vDisk, set a target device and booted it up. Â I opened the command prompt and&#8230;

<img class="aligncenter size-full wp-image-1506" src="http://theorypc.ca/wp-content/uploads/2016/06/8.png" alt="8" width="782" height="263" srcset="http://theorypc.ca/wp-content/uploads/2016/06/8.png 782w, http://theorypc.ca/wp-content/uploads/2016/06/8-300x101.png 300w, http://theorypc.ca/wp-content/uploads/2016/06/8-768x258.png 768w" sizes="(max-width: 782px) 100vw, 782px" /> 

What the heck? Â For some reason mounting my vDisk offline and I can&#8217;t change the shortname. Â I get access denied trying to change the shortname when the server is online so I was hoping offline would work. Â Unfortunately, it does not appear to be so.

So what is the solution? Â I guess repair office to force registration and it will point to the new paths because it will re-register all components. ğŸ™

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->