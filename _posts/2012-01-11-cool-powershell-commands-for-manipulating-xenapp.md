---
id: 689
title: Cool PowerShell commands for manipulating XenApp
date: 2012-01-11T12:08:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/01/11/cool-powershell-commands-for-manipulating-xenapp/
permalink: /2012/01/11/cool-powershell-commands-for-manipulating-xenapp/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/01/cool-powershell-commands-for.html
blogger_internal:
  - /feeds/7977773/posts/default/387377282209886738
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - scripting

---
After installing the & 6 SDK you can do some neat PowerShell scripts to help move things around. I recently created a test farm and needed a way to move all the applications and their settings from the original farm. These commands did it:

First: Export the & configuration

<http://www.jasonconger.com/post/migrate-citrix-&-6-folder-structure-using-powershell/>  
<http://www.jasonconger.com/post/export-and-import-citrix-&-6-published-applications-using-powershell/>

<pre class="lang:ps decode:true ">Get-XAApplicationReport * | Export-Clixml c:\testingApps.xml</pre>

Copy C:testingapps.xml to the new server.  
Create the folder structure:

<pre class="lang:ps decode:true ">Import-Clixml c:\testingapps.xml | New-XAFolder</pre>

Load your previous application settings:

<pre class="lang:ps decode:true ">Import-Clixml c:\testingApps.xml | New-XAApplication -WorkerGroupNames $null</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->