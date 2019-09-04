---
id: 695
title: Change file shares via scripting
date: 2011-09-15T08:02:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/09/15/change-file-shares-via-scripting/
permalink: /2011/09/15/change-file-shares-via-scripting/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/09/change-file-shares-via-scripting.html
blogger_internal:
  - /feeds/7977773/posts/default/2328488352184851285
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
I&#8217;ve come across a problem where users are filling up their hard disks and we need to move the highest utilization users to a new disk. In order to accomplish this I&#8217;ve setup a robocopy to move their files to a new disk and have it constantly mirrored until after-hours; where we run this script to move the file shares:

> <pre class="lang:batch decode:true  ">:backup original shares:
reg export "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\lanmanserver\Shares" C:\shares-backup.reg /y

setlocal enabledelayedexpansion
:what we need to do is grab the user name and the key...
for /f "tokens=1-2*" %%A IN ('reg query "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\lanmanserver\Shares" ^| findstr /I /C:"E:\User Files\Corporate" ^| sed.exe "s/Path=E:\\User Files\\Corporate/Path=G:\\User Files\\Corporate/"') DO (
echo reg add HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\lanmanserver\Shares /v %%A /t %%B /D "%%C" /f
)

for /f "tokens=1-2*" %%A IN ('reg query "HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\lanmanserver\Shares" ^| findstr /I /C:"E:\User Files\Finance" ^| sed.exe "s/Path=E:\\User Files\\Finance/Path=G:\\User Files\\Finance/"') DO (
echo reg add HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\lanmanserver\Shares /v %%A /t %%B /D "%%C" /f
)

net stop server /y
net start server</pre>

What this script does is:  
1) Backs up the existing share structure  
2) Queries the file shares for the specific path of the share we&#8217;re going to move  
3) Using SED.exe we change the drive letter from E: to G:  
4) Using reg.exe we overwrite the registry key with the new value  
5) we then stop and restart the server service to get the new shares working.

And we set that up as a scheduled task to run after-hours 🙂

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->