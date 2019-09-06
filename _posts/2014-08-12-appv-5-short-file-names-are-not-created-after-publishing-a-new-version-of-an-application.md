---
id: 600
title: AppV 5 - Short file names are not created after publishing a new version of an application
date: 2014-08-12T10:33:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/12/appv-5-short-file-names-are-not-created-after-publishing-a-new-version-of-an-application/
permalink: /2014/08/12/appv-5-short-file-names-are-not-created-after-publishing-a-new-version-of-an-application/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/appv-5-short-file-names-are-not-created.html
blogger_internal:
  - /feeds/7977773/posts/default/4882815560152152761
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
&nbsp;

<div style="-webkit-text-stroke-width: 0px; background-color: white; border: 0px; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 0.9em; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: 20.162017822265625px; margin: 10px 0px 0px; orphans: auto; outline: 0px; padding: 0px; text-align: left; text-indent: 0px; text-transform: none; white-space: normal; widows: auto; word-spacing: 0px;">
</div>

&nbsp;

<div style="-webkit-text-stroke-width: 0px; background-color: white; border: 0px; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: 20.162017822265625px; margin: 0px; orphans: auto; outline: 0px; padding: 0px; text-align: left; text-indent: 0px; text-transform: none; white-space: normal; widows: auto; word-spacing: 0px;">
  <div style="border: 0px; clear: right; font-family: inherit; font-size: 1em; font-style: inherit; font-weight: inherit; margin: 0px 0px 2em; outline: 0px; overflow: auto; padding: 0px;">
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      Our AppV packages are loaded on Citrix PVS servers. Â The PVS server is clean, and on each restart (restarts once a day) it will contact a AppV publishing server and pull down and mount the packages.
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      We have an application that makes file system calls in the 8.3 space (awesome, I know). Â When launching the application I can see a trace of it hitting 8.3 name spaces (via Procmon) and the application works without issue. Â So, we required a tweak to the package, I added to the management server, published the application and refreshed on the AppV Client, saw the new package get loaded and ran the application. Â Only now when I trace via Procmon.exe I can see that it's using Long File Names instead of the 8.3 name space and the program errors out because it's trying to find a .exe with the short name. Â I can confirm the original published package has the 8.3 namespace via dir /x and the new version does NOT.
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      <img style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0.3em 0px 0px; max-width: 98%; outline: 0px; padding: 0px; text-decoration: none;" src="http://social.technet.microsoft.com/Forums/getfile/500990" alt="8 dot 3 is enabled for the volume" />
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      <img style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0.3em 0px 0px; max-width: 98%; outline: 0px; padding: 0px; text-decoration: none;" src="http://social.technet.microsoft.com/Forums/getfile/500991" alt="" />
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      8 dot 3 exists for folders but not files...
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
      In the image above, the version 1E65...... was published to the server first and EE168.... is the new version published later in the day.
    </div>
    
    <div style="border: none; list-style-type: none; margin-bottom: 1em; outline: 0px; padding: 0px;">
      I have VFS folders in this package and 8.3 names are not created for the new package either. Â :/
    </div>
    
    <div style="border: none; list-style-type: none; margin-bottom: 1em; outline: 0px; padding: 0px;">
    </div>
    
    <div style="border: none; list-style-type: none; margin-bottom: 1em; outline: 0px; padding: 0px;">
      <img style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0.3em 0px 0px; max-width: 98%; outline: 0px; padding: 0px;" src="http://social.technet.microsoft.com/Forums/getfile/501488" alt="" />
    </div>
    
    <div style="border: none; list-style-type: none; margin-bottom: 1em; outline: 0px; padding: 0px;">
    </div>
    
    <div style="-webkit-text-stroke-width: 0px; background-color: white; border: 0px; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 0.9em; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: 20.162017822265625px; margin: 10px 0px 0px; orphans: auto; outline: 0px; padding: 0px; text-align: left; text-indent: 0px; text-transform: none; white-space: normal; widows: auto; word-spacing: 0px;">
    </div>
    
    <div style="-webkit-text-stroke-width: 0px; background-color: white; border: 0px; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: normal; letter-spacing: normal; line-height: 20.162017822265625px; margin: 0px; orphans: auto; outline: 0px; padding: 0px; text-align: left; text-indent: 0px; text-transform: none; white-space: normal; widows: auto; word-spacing: 0px;">
      <div style="border: 0px; clear: right; font-family: inherit; font-size: 1em; font-style: inherit; font-weight: inherit; margin: 0px 0px 2em; outline: 0px; overflow: auto; padding: 0px;">
        While attempting to create a 8.3 file name manually I got a NAME COLLISION and a failure to create it(?)<img style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0.3em 0px 0px; max-width: 98%; outline: 0px; padding: 0px; text-decoration: none;" src="http://social.technet.microsoft.com/Forums/getfile/501489" alt="" />
      </div>
      
      <div style="border: 0px; clear: right; font-family: inherit; font-size: 1em; font-style: inherit; font-weight: inherit; margin: 0px 0px 2em; outline: 0px; overflow: auto; padding: 0px;">
        So it appears if you have an application that does not properly support LFN (Long File Names) then it may not operate correctly if you update an AppV package, at least until the next restart (for some reason for us it creates the LFN after that).
      </div>
    </div>
    
    <div style="border: none; font-family: inherit; font-style: inherit; font-weight: inherit; list-style-type: none; margin: 0px 0px 1em; outline: 0px; padding: 0px; text-decoration: none;">
    </div>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->