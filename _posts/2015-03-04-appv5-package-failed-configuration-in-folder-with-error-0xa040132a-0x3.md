---
id: 575
title: AppV5 - Package failed configuration in folder with error 0xA040132A-0x3
date: 2015-03-04T12:15:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/03/04/appv5-package-failed-configuration-in-folder-with-error-0xa040132a-0x3/
permalink: /2015/03/04/appv5-package-failed-configuration-in-folder-with-error-0xa040132a-0x3/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/03/appv5-package-failed-configuration-in.html
blogger_internal:
  - /feeds/7977773/posts/default/5255556009552316854
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
When publishing AppV5 applications on a PVS server we sometimes encounter an issue where packages do not load.  There are usually events logged to event viewer with the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-gkwnyioz_v0/VPdKegmNHEI/AAAAAAAAAwA/1KJHWYUZ0-4/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.09.49%2BAM.png"><img src="http://2.bp.blogspot.com/-gkwnyioz_v0/VPdKegmNHEI/AAAAAAAAAwA/1KJHWYUZ0-4/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.09.49%2BAM.png" width="320" height="138" border="0" /></a>
</div>

Package {5075e8a4-4335-4101-991e-be88f5862575} version {940e4d49-af37-428c-a129-3bd37e3e4539} failed configuration in folder 'D:\AppVData\PackageInstallationRoot\5075E8A4-4335-4101-991E-BE88F5862575\940E4D49-AF37-428C-A129-3BD37E3E4539' with error 0xA040132A-0x3. with error 0xA040132A-0x3.

With another event that follows:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-_s1MC_aVUro/VPdKel39EnI/AAAAAAAAAv8/lWe5XVu_Pz0/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.09.34%2BAM.png"><img src="http://1.bp.blogspot.com/-_s1MC_aVUro/VPdKel39EnI/AAAAAAAAAv8/lWe5XVu_Pz0/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.09.34%2BAM.png" width="320" height="139" border="0" /></a>
</div>

Part or all packages publish failed.  
published: 14  
failed: 10  
Please check the error events of 'Configure/Publish Package' before this message for the details of the failure.

This issue is typically caused by two factors: folders not existing in the "PackageInstallationRoot", in my example that is D:\AppVData\PackageInstallationRoot.  You can find this value in the registry here:  
HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming /v PackageInstallationRoot /d D:\AppVData\PackageInstallationRoot

and registry entries existing here:  
HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-pZ8XADAD4U0/VPdLJgrclHI/AAAAAAAAAwM/YOiuRWDCDPg/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.05.42%2BAM.png"><img src="http://4.bp.blogspot.com/-pZ8XADAD4U0/VPdLJgrclHI/AAAAAAAAAwM/YOiuRWDCDPg/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.05.42%2BAM.png" width="320" height="213" border="0" /></a>
</div>

&nbsp;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-9HQu--PpZjI/VPdKQQAZSxI/AAAAAAAAAvs/b5h04mXIZ_M/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.05.48%2BAM.png"><img src="http://3.bp.blogspot.com/-9HQu--PpZjI/VPdKQQAZSxI/AAAAAAAAAvs/b5h04mXIZ_M/s1600/Screen%2BShot%2B2015-03-04%2Bat%2B11.05.48%2BAM.png" width="312" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      There are more values in the registry then the folder above.
    </td>
  </tr>
</table>

What you will find is a mismatch between the number of keys in Packages and the number of folders in your PackageInstallationRoot path.  For us, our error was we had 14 folders existing in that path, but 10 of them were missing.  But we had all 24 values existing in the registry.

To fix this error I created a script to create the missing folders that were found in the registry and doing a get-appvpublishingserver | sync-appvpublishingserver

<pre class="lang:batch decode:true ">D:
cd D:\AppVData\PackageInstallationRoot
for /f "tokens=1-6" %%a IN ('reg query HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AppV\Client\Streaming\Packages /s ^| findstr /i /c:"PackageRoot"') DO mkdir %%c</pre>

And then all applications were able to be published without issue.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->