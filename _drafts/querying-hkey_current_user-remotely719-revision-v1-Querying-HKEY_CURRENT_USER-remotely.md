---
id: 1206
title: Querying HKEY_CURRENT_USER remotely
date: 2016-04-25T11:58:09-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/719-revision-v1/
permalink: /2016/04/25/719-revision-v1/
---
I&#8217;m not sure why MS doesn&#8217;t allow this to work with their reg.exe program. On most workstations only a single user is logged in anyways&#8230;

<div>
</div>

<div>
  <div>
  </div>
  
  <div>
    <pre class="lang:batch decode:true ">for /f %%A IN ('type domain-list.txt') DO (
ECHO %%A &gt;&gt; list1.txt
reg query "\\%%A\HKU" &gt; ".\temp.txt"
findstr /R /C:"HKEY_USERS\\S-1-5-21.*[0-9]$" ".\temp.txt" &gt; ".\temp2.txt"
FOR /F %%Z IN ('TYPE .\temp2.txt') DO reg query "\\%%A\%%Z\Software\Unicus Medical Systems" /s /v ReportPrinterName | findstr /R /C:"ReportPrinterName" &gt;&gt; list1.txt
)</pre>
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
  Ok, what this does&#8230;
</div>

<div>
  1) Sequentially pull computer names from a file (domain-list.txt)
</div>

<div>
  2) Echo that computer name into our master &#8220;list&#8221; text file
</div>

<div>
  3) Query the HKEY_USERS via the computer name pulled from step 1 and save to a temporary file
</div>

<div>
  4) Execute a findstr that will only search for user accounts (and not CLASS keys) and save to &#8220;temp2.txt&#8221;.
</div>

<div>
  5) In Temp2.txt, parse and save as variable &#8220;%%Z&#8221; and execute our reg query and save to our list1.txt file.
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