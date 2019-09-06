---
id: 558
title: AppV5 - Recipe for ScreenTest III
date: 2015-07-03T00:53:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/03/appv5-recipe-for-screentest-iii/
permalink: /2015/07/03/appv5-recipe-for-screentest-iii/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/appv5-recipe-for-screentest-iii.html
blogger_internal:
  - /feeds/7977773/posts/default/1187365350934438842
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
This is the most recent app I sequenced and a good template for how I am going to do my recipes.

Prerequisites:  
[AppV5 - Sequencing first steps](http://trentent.blogspot.ca/2015/07/appv5-sequencing-first-steps.html)

Recipe:

I create install.cmd files for all of my applications so that, if required in the future, I can re-sequence an application quickly completely through script or via one of those 'PowerShell AppV5 automated GUI's'.

<span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">install.cmd</span>

<div>
  <pre class="lang:batch decode:true ">:: ScreenTest III Install Script
::
:: By Trentent Tye
::
:: packagename: SoftworksGroup_ScreenTestIII_1.6.5_x64
 
 
pushd %~dp0
 
msiexec.exe /i "Screen Setup.msi" /qb
 
cd "SAP Crystal Reports 64bit"
msiexec.exe /i "CRRuntime_64bit_13_0_14.msi" TRANSFORMS=SAP_DIR_Short2.mst INSTALLDIR="C:\Program Files (x86)\1" /qb
 
cd %~dp0
 
cd "ST3 fonts"
fontreg.exe /copy
 
cd %~dp0
 
regedit /s odbc.reg
 
popd
 
::Create and move shortcuts to the desired locations
mkdir "C:\Users\Public\Desktop\MyApps\Softworks Group"
mkdir "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Softworks Group"
copy /y "C:\Users\Public\Desktop\ScreenTest.lnk" "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Softworks Group\ScreenTest.lnk"
copy /y "C:\Users\Public\Desktop\ScreenTest.lnk" "C:\Users\Public\Desktop\MyApps\Softworks Group\ScreenTest.lnk"
del /q "C:\Users\Public\Desktop\ScreenTest.lnk"</pre>
</div>

<div>
  Supplemental files:
</div>

<div>
</div>

<div>
  <span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">odbc.reg</span>
</div>

<div>
  <pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00
 
[HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI\scr_test]
"Driver"="C:\\Windows\\system32\\SQLSRV32.dll"
"Server"="SERVER, 49282"
"LastUser"="screen_report"</pre>
</div>

<div>
  <div>
  </div>
</div>

<div>
  <a href="http://code.kliu.org/misc/fontreg/"><span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">Fontreg.exe</span></a>
</div>

<div>
  <span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">Â </span>
</div>

<div>
  <a href="http://trentent.blogspot.ca/2015/06/crystalreports-13-and-appv-5-have-issues_26.html"><span style="font-family: Helvetica Neue, Arial, Helvetica, sans-serif;">SAP_DIR_Short2.mst</span></a>
</div>

<div>
  <span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">Â </span>
</div>

<div>
  <span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">Â </span>
</div>

<div>
  1) Select 'install.cmd' and click 'Next'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Ji_uGW-m8oA/VZYrDQq7OzI/AAAAAAAAA3E/_4-YtJzgyBk/s1600/20.PNG"><img src="http://1.bp.blogspot.com/-Ji_uGW-m8oA/VZYrDQq7OzI/AAAAAAAAA3E/_4-YtJzgyBk/s320/20.PNG" width="320" height="224" border="0" /></a>
</div>

<div>
</div>

<div>
  2) Name the package and click 'Next'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-m9p5P4o8LxI/VZYrQx4SZXI/AAAAAAAAA3M/q9EZ6JOj_Nc/s1600/21.PNG"><img src="http://4.bp.blogspot.com/-m9p5P4o8LxI/VZYrQx4SZXI/AAAAAAAAA3M/q9EZ6JOj_Nc/s320/21.PNG" width="320" height="224" border="0" /></a>
</div>

<div>
  3) Let the install script do its thing...</p> 
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-GlODZ1nptF0/VZYt_QTX4hI/AAAAAAAAA3Y/LVdc9m2Zhz4/s1600/2015-07-03%2B00_37_40.gif"><img src="http://2.bp.blogspot.com/-GlODZ1nptF0/VZYt_QTX4hI/AAAAAAAAA3Y/LVdc9m2Zhz4/s640/2015-07-03%2B00_37_40.gif" width="640" height="504" border="0" /></a>
  </div>
  
  <p>
    4) AppV5 - <a href="http://trentent.blogspot.com/2015/07/appv5-post-install-sequencing-steps.html">Post install sequencing steps</a>
  </p>
  
  <p>
    5) Review for any extra registry keys/files (generally there are none or very few) and remove and save the package.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->