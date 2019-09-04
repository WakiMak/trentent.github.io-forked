---
id: 567
title: 'APPV5 &#8211; Sequencing FolioView ICD/CCI with AppV5.  (Issues with VFS not redirecting)'
date: 2015-03-24T13:53:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/24/appv5-sequencing-folioview-icdcci-with-appv5-issues-with-vfs-not-redirecting/
permalink: /2015/03/24/appv5-sequencing-folioview-icdcci-with-appv5-issues-with-vfs-not-redirecting/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/appv5-sequencing-folioview-icdcci-with.html
blogger_internal:
  - /feeds/7977773/posts/default/6494594314097681980
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
When sequencing FolioView 2012 or 2015 with AppV5 SP3, launching the application after sequencing generates an error:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-A4g184Oj1b8/VRG_WgwWmNI/AAAAAAAAAws/_iEW67cmP7k/s1600/FolioView_Error.PNG"><img src="http://2.bp.blogspot.com/-A4g184Oj1b8/VRG_WgwWmNI/AAAAAAAAAws/_iEW67cmP7k/s1600/FolioView_Error.PNG" width="320" height="135" border="0" /></a>
</div>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
Folio Views  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
No object handler is registered for this object type.  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
OK  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

<div>
</div>

<div>
  This error seems to appear because VFS either isn&#8217;t fully loaded by the time Views.exe is loaded so the program/dlls actually traverse down a non-virtual path, or they somehow break the AppV bubble and don&#8217;t find whatever it is they are looking for. Â To correct this issue, you need to create a symbolic link to the VFS&#8217;d program directory from where the actual directory is supposed to reside. Â For instance, I sequenced FolioView to:
</div>

<div>
</div>

<div>
  C:\Program Files (x86)\CIHI
</div>

<div>
</div>

<div>
  To produce the error, I launch my command like so (this is what AppV5 automatically puts into the shortcut):
</div>

<div>
</div>

<div>
  <div>
    %ALLUSERSPROFILE%\Microsoft\AppV\Client\Integration\C05CC38A-D146-442B-B97F-89E8867DF0CE\Root\VFS\ProgramFilesX86\CIHI\CIHI_PUB_2015\Cihi32\Views.exe -L1033 -cSoftware\CIHI\2015\ICD10\English -i&#8221;\\fileserver\CTX_APPS\FolioView2015\cci_2015_eng.sdw&#8221;
  </div>
  
  <div>
  </div>
</div>

<div>
  I then get the error message.
</div>

<div>
</div>

<div>
  If I mklink /d &#8220;C:\Program Files (x86)\CIHI&#8221; D:\AppVData\PackageInstallationRoot\C05CC38A-D146-442B-B97F-89E8867DF0CE\0CA628F4-AC24-4981-9B52-DA
</div>

<div>
  B4445B8ECF\Root\VFS\ProgramFilesX86\CIHI
</div>

<div>
</div>

<div>
  Then launch the application via the &#8216;native&#8217; path:
</div>

<div>
</div>

<div>
  &#8220;C:\Program Files (x86)\CIHI\CIHI_PUB_2015\Cihi32\Views.exe&#8221; Â -L1033 -cSoftware\CIHI\2015\ICD10\English -i&#8221;\\q9-v-citrix-lif1.healthy.bewell.ca\CTX_APPS\FolioView2015\cci_2015_eng.sdw&#8221;
</div>

<div>
</div>

<div>
  It works without any error messages and without issue.
</div>

<div>
</div>

<div>
  Here&#8217;s a short clip of the error and the &#8216;fix&#8217;:
</div>

<div>
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-TuxAGVp1z4I/VRHAhR550LI/AAAAAAAAAw0/UUp-Q1MBqV4/s1600/FolioView_Error.gif"><img src="http://4.bp.blogspot.com/-TuxAGVp1z4I/VRHAhR550LI/AAAAAAAAAw0/UUp-Q1MBqV4/s1600/FolioView_Error.gif" width="640" height="512" border="0" /></a>
  </div>
</div>

<div>
</div>

<div>
  I did a stack trace of the error message and it comes up like so:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-8NdihK7zIRM/VRG64nc_9EI/AAAAAAAAAwg/HBdFtT8QvPY/s1600/Stack_Trace.PNG"><img src="http://1.bp.blogspot.com/-8NdihK7zIRM/VRG64nc_9EI/AAAAAAAAAwg/HBdFtT8QvPY/s1600/Stack_Trace.PNG" width="640" height="513" border="0" /></a>
</div>

<div>
  There are several calls that appear to look for files (nfomgr4, fcctrl4, mpr, davInt) that maybe generating the error message, but, I am not skilled enough at WinDBG at this time to be able to dig deeper. Â Either way, there appears to be a limitation within AppV5 that prevents this program from operating correctly. Â The same program works in AppV 4.6 without issue though, so it appears AppV5 sill has some work to go to get full compatibility with PITA applications.
</div>

<div>
</div>

<div>
  Here&#8217;s the application working fine in AppV 4.6. Â The application was installed identically for both AppV versions.
</div>

<div>
</div>

<div>
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-4pKt-O-uv3k/VRHAtWkhEfI/AAAAAAAAAw8/hBT5EauWXX8/s1600/Folio_4.6_works.gif"><img src="http://3.bp.blogspot.com/-4pKt-O-uv3k/VRHAtWkhEfI/AAAAAAAAAw8/hBT5EauWXX8/s1600/Folio_4.6_works.gif" width="640" height="426" border="0" /></a>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->