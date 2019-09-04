---
id: 717
title: Getting NTBackup working in WinPE 3.0
date: 2010-10-14T01:07:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2010/10/14/getting-ntbackup-working-in-winpe-3-0/
permalink: /2010/10/14/getting-ntbackup-working-in-winpe-3-0/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2010/10/getting-ntbackup-working-in-winpe-30.html
blogger_internal:
  - /feeds/7977773/posts/default/6346923816296746739
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
---
This is pretty simple. Install the [NTBackup for Windows 7](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=a71845fd-4496-439c-ab31-be73498ad3fe&displaylang=en) on to a Windows 7 computer. Then copy the appropriate file to their respective places on your WinPE image. The files:

<div>
</div>

<div>
  <div>
    X:\Windows\System32\explorer.exe
  </div>
  
  <div>
    X:\Windows\System32\NTBackupRestoreUtility.exe
  </div>
  
  <div>
    X:\Windows\System32\Query.dll
  </div>
  
  <div>
    X:\Windows\System32\en-US\NTBackupRestoreUtility.exe.mui
  </div>
</div>

<div>
</div>

<div>
  That&#8217;s it!
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->