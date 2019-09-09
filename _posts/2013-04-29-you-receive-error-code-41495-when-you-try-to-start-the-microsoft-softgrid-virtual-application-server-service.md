---
id: 651
title: You receive error code 41495 when you try to start the Microsoft SoftGrid Virtual Application Server service
date: 2013-04-29T11:22:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/04/29/you-receive-error-code-41495-when-you-try-to-start-the-microsoft-softgrid-virtual-application-server-service/
permalink: /2013/04/29/you-receive-error-code-41495-when-you-try-to-start-the-microsoft-softgrid-virtual-application-server-service/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/04/you-receive-error-code-41495-when-you.html
blogger_internal:
  - /feeds/7977773/posts/default/7904763236991241939
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
Microsoft has a KB article on this error message:

<pre class="lang:default decode:true ">http://support.microsoft.com/kb/931165</pre>

One of the notes in the KB articles states:  
**Note:** If the SoftGrid Virtual Application Server is version 3.x, the DNS host name must contain the NetBIOS name.

I suspect this note should say:  
**Note:** If the SoftGrid Virtual Application Server is version 3.x or greater, the DNS host name must contain the NetBIOS name.

As when we used the FQDN on our Management Server for the Default group we got this error message. When we changed it to the NetBIOS name the error was successfully resolved and did not reappear.

I was able to confirm using procmon that when starting the AppV Server Management Service that it queried this registry key:

<pre class="lang:reg decode:true ">HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\ComputerName\ActiveComputerName</pre>

to set the computer name. This key contains the NetBIOS name.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->