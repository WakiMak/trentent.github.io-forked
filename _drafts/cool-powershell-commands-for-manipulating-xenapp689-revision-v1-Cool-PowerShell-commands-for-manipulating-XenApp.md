---
id: 1181
title: Cool PowerShell commands for manipulating XenApp
date: 2016-04-25T11:22:16-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/689-revision-v1/
permalink: /2016/04/25/689-revision-v1/
---
After installing the XenApp 6 SDK you can do some neat PowerShell scripts to help move things around. I recently created a test farm and needed a way to move all the applications and their settings from the original farm. These commands did it:

First: Export the XenApp configuration

<http://www.jasonconger.com/post/migrate-citrix-xenapp-6-folder-structure-using-powershell/>  
<http://www.jasonconger.com/post/export-and-import-citrix-xenapp-6-published-applications-using-powershell/>

<pre class="lang:ps decode:true ">Get-XAApplicationReport * | Export-Clixml c:\testingApps.xml</pre>

Copy C:testingapps.xml to the new server.  
Create the folder structure:

<pre class="lang:ps decode:true ">Import-Clixml c:\testingapps.xml | New-XAFolder</pre>

Load your previous application settings:

<pre class="lang:ps decode:true ">Import-Clixml c:\testingApps.xml | New-XAApplication -WorkerGroupNames $null</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->