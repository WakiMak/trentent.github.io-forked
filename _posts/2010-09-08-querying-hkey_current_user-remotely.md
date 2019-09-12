---
id: 719
title: Querying HKEY_CURRENT_USER remotely
date: 2010-09-08T14:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2010/09/08/querying-hkey_current_user-remotely/
permalink: /2010/09/08/querying-hkey_current_user-remotely/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2010/09/querying-hkeycurrentuser-remotely.html
blogger_internal:
  - /feeds/7977773/posts/default/4489632001526514139
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
I'm not sure why MS doesn't allow this to work with their reg.exe program. On most workstations only a single user is logged in anyways...

<div>
</div>

<div>
  <div>
  </div>
  
  <div>
```shell
for /f %%A IN ('type domain-list.txt') DO (
ECHO %%A >> list1.txt
reg query "\\%%A\HKU" > ".\temp.txt"
findstr /R /C:"HKEY_USERS\\S-1-5-21.*[0-9]$" ".\temp.txt" > ".\temp2.txt"
FOR /F %%Z IN ('TYPE .\temp2.txt') DO reg query "\\%%A\%%Z\Software\Unicus Medical Systems" /s /v ReportPrinterName | findstr /R /C:"ReportPrinterName" >> list1.txt
)
```
  </div>
</div>

<div>
</div>

<div>
  The above may have truncated (FYI).
</div>

<div>
</div>

<div>
  Ok, what this does...
</div>

<div>
  1) Sequentially pull computer names from a file (domain-list.txt)
</div>

<div>
  2) Echo that computer name into our master "list" text file
</div>

<div>
  3) Query the HKEY_USERS via the computer name pulled from step 1 and save to a temporary file
</div>

<div>
  4) Execute a findstr that will only search for user accounts (and not CLASS keys) and save to "temp2.txt".
</div>

<div>
  5) In Temp2.txt, parse and save as variable "%%Z" and execute our reg query and save to our list1.txt file.
</div>

<div>
</div>

<div>
  You can replace anything with step 5 to do whatever you need (Reg Add/Delete/Query/etc.)
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->