---
id: 601
title: AppV 5 - .NetFramework applications that only work with 32bit .NetFramework architecture
date: 2014-08-05T11:29:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/05/appv-5-netframework-applications-that-only-work-with-32bit-netframework-architecture/
permalink: /2014/08/05/appv-5-netframework-applications-that-only-work-with-32bit-netframework-architecture/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/appv-5-netframework-applications-that.html
blogger_internal:
  - /feeds/7977773/posts/default/3416558821540727145
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
I encountered an issue with an application of ours that refused to launch on Windows 2008 R2 SP2 but worked on Windows 2003 R2. ¬ I did a procmon.exe trace and noticed some discrepancies in the trace results:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-T2uibsg2gzg/U-EQnK4a7uI/AAAAAAAAAdo/QkgNZJapU94/s1600/2008-64.png"><img src="http://4.bp.blogspot.com/-T2uibsg2gzg/U-EQnK4a7uI/AAAAAAAAAdo/QkgNZJapU94/s1600/2008-64.png" width="320" height="34" border="0" /></a>
</div>

HKLM\SOFTWARE\Microsoft\Fusion\NativeImagesIndex\v2.0.50727_64  
^^ 2008 R2 SP2 (non-working application launches)

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-bXkyNwcoLmo/U-EQliPUwSI/AAAAAAAAAdg/ZxdjJCSypwQ/s1600/2003-32.png"><img src="http://2.bp.blogspot.com/-bXkyNwcoLmo/U-EQliPUwSI/AAAAAAAAAdg/ZxdjJCSypwQ/s1600/2003-32.png" width="320" height="51" border="0" /></a>
</div>

HKLM\SOFTWARE\Microsoft\Fusion\NativeImagesIndex\v2.0.50727_32  
^^ 2003 R2 (working application launches)

So, my application was defaulting to the .NetFramework64 architecture and no launching. ¬ To correct this I was able to run this command:

<div>
  <div>
    If you have a 64 bit machine and want to run a .NET application that only works with the 32 bit CLR you would have to make changes to the .NET framework on your machine
  </div>
  
  <div>
    Set the .NET framework to load the CLR in WOW mode through this command
  </div>
  
  <div>
    Open up command prompt and type this command
  </div>
  
  <div>
    C:\WINDOWS\Microsoft.NET\Framework64\v2.0.50727\Ldr64.exe SetWow
  </div>
  
  <div>
    Now you should be able to run apps that use only the .NET 32 bit CLR.
  </div>
  
  <div>
    To revert back to the default 64 bit framework run
  </div>
  
  <div>
    C:\WINDOWS\Microsoft.NET\Framework64\v2.0.50727\Ldr64.exe Set64
  </div>
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
  (<a href="http://techdhaan.wordpress.com/2010/03/15/run-32-bit-net-applications-on-64-bit-machines/">source</a>)
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
  It appears all this does is modify one registry key:
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
  HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework<br /> REG_DWORD Enable64bit<br /> DATA 0x0 (for SetWow) 0x1 (for Set64)
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
  Alternatively, run corflags.exe against your executable to force it only utilize the 32bit .NetFramework. ¬ Corflags.exe can be gotten from the "<a href="http://www.microsoft.com/en-us/download/confirmation.aspx?id=15354">.NET Framework 2.0 Software Development Kit (SDK) (x64)</a>"
</div>

<div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
  Execute the application with the following:
</div>

‚Äúc:\Program Files\Microsoft SDKs\Windows\v6.0A\Bin\x64\CorFlags.exe‚Äù MyApplication.exe /32bit+

<div>
  <div style="background-attachment: initial; background-clip: initial; background-image: initial; background-origin: initial; background-position: initial; background-repeat: initial; background-size: initial; border: 0px; clear: both; margin-bottom: 1em; padding: 0px; vertical-align: baseline;">
    <span style="font-family: Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif; font-size: 14px; line-height: 17.804800033569336px;">After doing so your application should only use the 32bit .NetFramework. ¬ And, if you are in my scenario, the app now works without issue.</span></p> 
    
    <p>
      :::::::::::::::::::::::EDIT::::::::::::::::::::::::::::::
    </p>
    
    <p>
      It appears the following registry key is for status only:
    </p>
    
    <p>
      HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework<br /> REG_DWORD Enable64bit<br /> DATA 0x0 (for SetWow) 0x1 (for Set64)
    </p>
    
    <p>
      It does not actually do anything configuration-wise. Changing this key does not manipulate the ability to .NetFramework to use 32bit or 64bit.
    </p>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->