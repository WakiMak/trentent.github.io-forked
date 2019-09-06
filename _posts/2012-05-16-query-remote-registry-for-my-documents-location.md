---
id: 677
title: Query remote registry for My Documents location
date: 2012-05-16T14:50:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/05/16/query-remote-registry-for-my-documents-location/
permalink: /2012/05/16/query-remote-registry-for-my-documents-location/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/05/common-question-i-get-is-can-we-move.html
blogger_internal:
  - /feeds/7977773/posts/default/7409766884714471726
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
A common question I get is, "Can we move all these users My Documents folder from Server A to Server B"?

"Sure," I'll respond, "we'll just update their AD home directory attribute and have them log off and log back on."

Inevitably, this will fail in some capacity.  The users don't wait for the copy to complete is an example and then it fails and the My Documents is still pointing to their old server.  To correct this issue you can pre-copy the files then when doing the login copy, folder redirection will only copy changed files.  This can still take a while but it's much faster then copying everything, especially with a big directory.

Eventually, I'll get asked, "we want to shut down the old server, can we verify that all the users my docs have been copied off and their computers are pointing to the correct location?"

In order to accomplish this effectively, I wrote a script that runs through a list of computers you give it and it checks the registry and presents you a list of all the network "My Documents" it finds.  This is the script:

<pre class="lang:batch decode:true ">:Find-redir.cmd

:This next bit will query the registry to see if they are redirecting already...
del /q "%temp%\redir.txt"


:you need to drop a list of computers on this file.


FOR /F "tokens=*" %%a IN ('type %1') DO (
  echo =============================================== >> "%temp%\redir.txt"
  echo %%a >> "%temp%\redir.txt"
  reg query \\%%a\HKU | findstr /V /C:"_Classes" | findstr /R /V /C:"S-1-5-1[89]" | findstr /R /V /C:"S-1-5-20" | findstr /v /c:".DEFAULT" | findstr /v /c:"!" | findstr  /c:"HKEY_USERS\S" > "%temp%\reg-user.txt"
  for /f "tokens=*" %%A IN ('type "%temp%\reg-user.txt"') DO reg query "\\%%a\%%A\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders" | findstr \\\\ >> "%temp%\redir.txt"
  echo =============================================== >> "%temp%\redir.txt"
)
notepad "%temp%\redir.txt"</pre>


To use the script; get a list of computers or IP addresses and then run the script as:  
find-redir.cmd "list-of-computers.txt"

The list of computers.txt can look like:  
192.168.1.1  
192.168.1.2  
Laptop1  
Laptop2

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->