---
id: 1498
title: Microsoft Office - Your mailbox is currently being optimized as part of upgrading to Outlook 2010
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

<img class="aligncenter size-full wp-image-1499" src="/wp-content/uploads/2016/06/1.png" alt="1" width="531" height="581" srcset="/wp-content/uploads/2016/06/1.png 531w, /wp-content/uploads/2016/06/1-274x300.png 274w" sizes="(max-width: 531px) 100vw, 531px" /> 

"Your mailbox is currently being optimized as part of upgrading to Outlook 2010.  This one time process may take over 15 minutes and performance may be affected while optimization is in progress."

"Upgrade Outlook Connector"

"You must upgrade to the latest version of Outlook Hotmail Connector to continue using this e-mail account."

In addition, all of the options under 'Account Settings' do nothing.  This is in addition to my Home/Inbox being empty and my calendar blank.  Attempting to create a calendar event results in:  
<img class="aligncenter size-full wp-image-1500" src="/wp-content/uploads/2016/06/2.png" alt="2" width="229" height="117" /> 

I then tried to open the 'Mail' control panel and found it was missing.  I manually opened it by launching it from:  
"C:\Program Files (x86)\Microsoft Office\Office14\MLCFG32.CPL"

<img class="aligncenter size-full wp-image-1501" src="/wp-content/uploads/2016/06/3.png" alt="3" width="413" height="271" srcset="/wp-content/uploads/2016/06/3.png 413w, /wp-content/uploads/2016/06/3-300x197.png 300w" sizes="(max-width: 413px) 100vw, 413px" /> 

From here, clicking any of the buttons resulted in nothing happening.  Email Accounts... Data Files... did nothing.  Clicking 'Show Profiles' did work, though.  So what the heck is going on?  I opened up Process Monitor to try and find out.  I opened up the 'Mail' control panel via the command line, since this is a 'Rundll32.exe' I set the procmon filter to 'Command Line' 'contains' 'MLCFG' and clicked one of the non-functioning buttons.  My result:

<img class="aligncenter size-full wp-image-1502" src="/wp-content/uploads/2016/06/4.png" alt="4" width="509" height="197" srcset="/wp-content/uploads/2016/06/4.png 509w, /wp-content/uploads/2016/06/4-300x116.png 300w" sizes="(max-width: 509px) 100vw, 509px" /><img class="aligncenter size-full wp-image-1503" src="/wp-content/uploads/2016/06/5.png" alt="5" width="766" height="161" srcset="/wp-content/uploads/2016/06/5.png 766w, /wp-content/uploads/2016/06/5-300x63.png 300w" sizes="(max-width: 766px) 100vw, 766px" /> 

PATH NOT FOUND.  Why isn't the path found?  I opened a command prompt and followed the path doing a dir /x to show shortnames:

<img class="aligncenter size-full wp-image-1504" src="/wp-content/uploads/2016/06/6.png" alt="6" width="776" height="263" srcset="/wp-content/uploads/2016/06/6.png 776w, /wp-content/uploads/2016/06/6-300x102.png 300w, /wp-content/uploads/2016/06/6-768x260.png 768w" sizes="(max-width: 776px) 100vw, 776px" /> 

Well, there is the issue.  It's showing as 'MICROS~4' when it's searching for 'MICROS~2'.  Doing a repair on office will cause re-registration to occur and this does resolve the issue, but I wanted to change my shortname instead of doing a repair.  Since these are PVS servers I created a new version, mounted the vDisk and changed the shortnames via fsutil:

<img class="aligncenter size-full wp-image-1505" src="/wp-content/uploads/2016/06/7.png" alt="7" width="679" height="445" srcset="/wp-content/uploads/2016/06/7.png 679w, /wp-content/uploads/2016/06/7-300x197.png 300w" sizes="(max-width: 679px) 100vw, 679px" /> 

In the screenshot, you can clearly see 'Microsoft Office' is now 'MICROS~2'.  I unmounted the vDisk, set a target device and booted it up.  I opened the command prompt and...

<img class="aligncenter size-full wp-image-1506" src="/wp-content/uploads/2016/06/8.png" alt="8" width="782" height="263" srcset="/wp-content/uploads/2016/06/8.png 782w, /wp-content/uploads/2016/06/8-300x101.png 300w, /wp-content/uploads/2016/06/8-768x258.png 768w" sizes="(max-width: 782px) 100vw, 782px" /> 

What the heck?  For some reason mounting my vDisk offline and I can't change the shortname.  I get access denied trying to change the shortname when the server is online so I was hoping offline would work.  Unfortunately, it does not appear to be so.

So what is the solution?  I guess repair office to force registration and it will point to the new paths because it will re-register all components.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->