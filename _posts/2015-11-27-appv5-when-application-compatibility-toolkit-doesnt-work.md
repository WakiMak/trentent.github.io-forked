---
id: 537
title: 'AppV5 &#8211; When Application Compatibility Toolkit doesn&#8217;t work'
date: 2015-11-27T14:12:00-06:00
author: trententtye
layout: post
permalink: /2015/11/27/appv5-when-application-compatibility-toolkit-doesnt-work/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/11/appv5-when-application-compatibility.html
blogger_internal:
  - /feeds/7977773/posts/default/8943011394075028607
image: /wp-content/uploads/2015/11/image001-1.jpeg
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
I have an application that embeds an IE window and generates a small HTML file for some content. Â This application has some&#8230;.. Â interesting&#8230;. Â configuration settings that require a folder with &#8220;EVERYONE:F&#8221; permissions. Â This folder cannot be in a path that contains spaces, cannot be a environment variable and the vendor recommends to put the folder on the root of the C: drive as C:TMP. Â To make it more crazy, when launching the application it will generate a folder under the C:TMP that is locked to the %CLIENTNAME% (e.g., C:TMPComputerName) and every login to the application generates unique files to that session, so technically, each folder underneath TMP is unique to the user. Â The vendor actually recommends completely deleting the contents of the TMP folder DAILY.

So why not just store these files in %TEMP% instead? Â Great question. Â If the vendor made that happen I wouldn&#8217;t have this lovely blog post.

(Just for a note this application utilized multiple folders on the root of C: as well, TMP being one, since we move our [PackageInstallationRoot](http://theorypc.ca/2014/08/14/appv-5-changing-packageinstallationroot-impacts-appvpackagedrive-variable/) to a different drive, TMP isn&#8217;t available on C:)

With that all said, what am I looking to solve?

Well, I don&#8217;t want to be creating folders on the root of the C: drive as this application will be on our &#8216;generic&#8217; PVS Citrix servers and minimizing the potential for pollution on the master image is a goal, having to create a folder and configure permissions is possible and I&#8217;ve done it before but it&#8217;s not very clean or elegant; really if we&#8217;re going to do that then we may as well pollute the C: drive. Â We want to maintain portability so creating a C:TMP folder on the master image prevents us from publishing this package on desktops or other systems in the future unless this is well documented requirement.

We&#8217;ve just learned a new trick though, we can try using Microsoft ACT to create a file path shim that redirects the path to a different one. Â So let&#8217;s do that&#8230;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-3VKB9ddJ9qk/Vlis_nosTgI/AAAAAAAABOU/pYzz3WH_8hQ/s1600/image001.jpeg"><img src="http://2.bp.blogspot.com/-3VKB9ddJ9qk/Vlis_nosTgI/AAAAAAAABOU/pYzz3WH_8hQ/s320/image001.jpeg" width="320" height="256" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      The problem.
    </td>
  </tr>
</table>

I created [the shim](http://theorypc.ca/2015/11/26/appv5-using-application-compatibility-toolkit-to-solve-issues/) using the steps from this post. Â (<span style="font-size: 13px; text-align: center;">C:TMP;C:ProgramDataMicrosoftAppVClientIntegrationD8E3DB68-4E48-4409-8E95-4354CC6E664BRootVFSProgramFilesX64dlc11.2TMP)</span>Â I then launched the application and&#8230;

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-1UVeWYnmlA4/VliwCZX1jnI/AAAAAAAABOo/YO3rXGc_WSc/s1600/image002.jpeg"><img src="http://2.bp.blogspot.com/-1UVeWYnmlA4/VliwCZX1jnI/AAAAAAAABOo/YO3rXGc_WSc/s320/image002.jpeg" width="320" height="256" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Huh. Â That doesn&#8217;t look right.
    </td>
  </tr>
</table>

And it didn&#8217;t work. Â I then used procmon.exe to examine to see if it was reading the file correctly:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-_asbxfryO20/VlixAh9nrMI/AAAAAAAABOw/KnP0EL34XcI/s1600/shim.png"><img src="http://3.bp.blogspot.com/-_asbxfryO20/VlixAh9nrMI/AAAAAAAABOw/KnP0EL34XcI/s640/shim.png" width="640" height="338" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      I see SUCCESS&#8230;
    </td>
  </tr>
</table>

Procmon.exe is reporting that it IS reading that HTM file correctly. Â So why isn&#8217;t it being displayed?

I ran across a similar issue on another application and what I found was that the embedded component appears to be running \*outside\* the bubble. Â Since the shim targets applications (in this case prowin32.exe) all reads from prowin32.exe are being redirected by other processes are not. Â I suspect (though I have no proof), in this case, that somehow the IE component is breaking outside the bubble and so it&#8217;s NOT getting redirected.

Can we force all programs to get redirected to the proper path? Â Yes, we can.

Using symbolic links we can force any access to the directory to be redirected to the AppV package path. Â AppV will then see this is a path within the &#8216;bubble&#8217; and redirect \*again\* to your local profile where the AppV5 writes will take place.

I removed the shim and then executed:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-HWawyw0qyL4/VlizDxKScnI/AAAAAAAABPA/JJf53zxh6aY/s1600/symbolic_link.png"><img src="http://3.bp.blogspot.com/-HWawyw0qyL4/VlizDxKScnI/AAAAAAAABPA/JJf53zxh6aY/s320/symbolic_link.png" width="320" height="53" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Symbolic Link<br /> mklink /d C:TMP C:ProgramDataMicrosoftAppVClientIntegrationD8E3DB68-4E48-4409-8E95-4354CC6E664BRootVFSProgramFilesX64dlc11.2TMP
    </td>
  </tr>
</table>

Tracing with procmon.exe now showed us this:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-vLlRXlLh6Tw/Vliz-2IBtxI/AAAAAAAABPM/SjgdGaOMU_c/s1600/working.png"><img src="http://3.bp.blogspot.com/-vLlRXlLh6Tw/Vliz-2IBtxI/AAAAAAAABPM/SjgdGaOMU_c/s640/working.png" width="640" height="406" border="0" /></a>
</div>

And the application now displayed this:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-WQbglMa19qk/VlitBgDTflI/AAAAAAAABOc/OzmFejhVkfo/s1600/image003.jpeg"><img src="http://3.bp.blogspot.com/-WQbglMa19qk/VlitBgDTflI/AAAAAAAABOc/OzmFejhVkfo/s320/image003.jpeg" width="320" height="250" border="0" /></a>
</div>

So the application is now working without any issues, all of these &#8216;temp&#8217; files are being redirected to a location where they will not be saved between sessions so no cleanup is ever needed. Â We need to add the mklink.exe to the DeploymentConfig.xml.

<pre class="lang:default decode:true ">&lt;machinescripts&gt;
 &lt;addpackage&gt;
  &lt;path&gt;C:\PublishedApplications\AHS-BDMPHARMACY-PREFLIGHT.cmd&lt;/Path&gt;
  &lt;arguments&gt;A&lt;/Arguments&gt;
  &lt;wait RollbackOnError="true" Timeout="30"/&gt;
 &lt;/AddPackage&gt;
 &lt;removepackage&gt;
  &lt;path&gt;C:\PublishedApplications\AHS-BDMPHARMACY-PREFLIGHT.cmd&lt;/Path&gt;
  &lt;arguments&gt;R&lt;/Arguments&gt;
  &lt;wait RollbackOnError="false" Timeout="60"/&gt;
 &lt;/RemovePackage&gt;
&lt;/MachineScripts&gt;</pre>

AHS-BDMPHARMACY-PREFLIGHT.CMD:

<pre class="lang:batch decode:true ">:: ===========================================================================================================
::
:: Created by:  Trentent Tye
::   Intel Server Team
::   IBM Canada Ltd.
::
:: Creation Date: Nov 27, 2015
::
:: File Name:  AHS-BDMPHARMACY-PREFLIGHT.CMD
::
:: Description:  Configures TMP folder for BDM Pharmacy
::
:: ===========================================================================================================
::created by Trentent Tye
::default
if [%1] EQU [] (
IF NOT EXIST C:\TMP (
  mklink /d C:\TMP C:\ProgramData\Microsoft\AppV\Client\Integration\D8E3DB68-4E48-4409-8E95-4354CC6E664B\Root\VFS\ProgramFilesX64\dlc11.2\TMP
  )
)
 
::add-package
if /I  [%1] EQU [A] (
  mklink /d C:\TMP C:\ProgramData\Microsoft\AppV\Client\Integration\D8E3DB68-4E48-4409-8E95-4354CC6E664B\Root\VFS\ProgramFilesX64\dlc11.2\TMP
)
 
::remove-package
if /I [%1] EQU [R] (
  rmdir /s /q C:\TMP
)</pre>

And we are now done.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->