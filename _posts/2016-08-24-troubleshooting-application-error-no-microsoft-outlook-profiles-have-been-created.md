---
id: 1641
title: Troubleshooting application error "No Microsoft Outlook profiles have been created"
date: 2016-08-24T10:40:38-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1641
permalink: /2016/08/24/troubleshooting-application-error-no-microsoft-outlook-profiles-have-been-created/
categories:
  - Blog
tags:
  - PowerShell
  - scripting
---
I was informed an AppV5 application was getting the following error message:

<img class="aligncenter size-full wp-image-1642" src="/wp-content/uploads/2016/08/NoMicrosoftOutlookProfilesHaveBeenCreated.png" alt="NoMicrosoftOutlookProfilesHaveBeenCreated" width="413" height="152" srcset="/wp-content/uploads/2016/08/NoMicrosoftOutlookProfilesHaveBeenCreated.png 413w, /wp-content/uploads/2016/08/NoMicrosoftOutlookProfilesHaveBeenCreated-300x110.png 300w" sizes="(max-width: 413px) 100vw, 413px" /> 

<pre class="">Microsoft Outlook

No Microsoft Outlook profiles have been created. In Microsoft Windows, click the Start button, and then click Control Panel. Click User Accounts, and then click Mail. Click Show Profiles, and then click Add.

OK 

</pre>

So what's going on here?

The application is trying to create an email message and needs to activate Outlook to add the attachment.  This error can be worked around by launching Outlook \*first\*, which creates our profile, but I would prefer to not launch programs to use resources in the background if it can be avoided.

&nbsp;

&nbsp;

What I'd like to do is silently create our Outlook profile and then continue launching the application.  I didn't find a particularly good solution for this, but I did eventually stumble across one with some minor modifications to meet my needs:

<pre class="lang:ps decode:true ">$MsOutlook = New-Object -ComObject Outlook.Application 
$Namespace = $MsOutlook.GetNamespace("MAPI") 
$Folder = $Namespace.GetDefaultFolder("olFolderInbox") 
$Explorer = $Folder.GetExplorer() 
$MsOutlook.quit()</pre>

Or via cmd.exe:

<pre class="lang:default decode:true">powershell.exe -executionPolicy byPass -command "&{ $MsOutlook = New-Object -ComObject Outlook.Application; $Namespace = $MsOutlook.GetNamespace(\"MAPI\"); $Folder = $Namespace.GetDefaultFolder(\"olFolderInbox\"); $Explorer = $Folder.GetExplorer(); $MsOutlook.quit() }"
</pre>

This powershell script will launch launch Outlook into the 'Inbox' and terminate.  Since it's done through 'embedded' commands the only thing you may see is a brief blip of Outlook with the 'embedded' icon in the taskbar.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->