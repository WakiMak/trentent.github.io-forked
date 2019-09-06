---
id: 523
title: AppV5 - Package {GUID} version {GUID} failed configuration in folder '%packageinstallationroot% with error 0x4C40310C-0x12
date: 2016-03-30T11:06:00-06:00
author: trententtye
layout: post
permalink: /2016/03/30/appv5-package-guid-version-guid-failed-configuration-in-folder-packageinstallationroot-with-error-0x4c40310c-0x12/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/03/appv5-pacakge-guid-version-guid-failed.html
blogger_internal:
  - /feeds/7977773/posts/default/7279608715158245541
image: /wp-content/uploads/2016/03/11-1.png
categories:
  - Blog
tags:
  - AppV
  - procmon
---
We experienced this error on a package on one of our Citrix servers in the AppVClientAdmin event logs. Â Attempting to procmon this error didn't reveal anything substantial for what could be causing this issue. Â [I then tried my last post to enable AppV debug logs to see if we can see what's going on](http://theorypc.ca/2016/03/24/appv5-the-trouble-with-appv5-logs-and-a-solution/).

Because this server was being used by other users the log generated a lot of noise. Â I stopped the log as soon as I got the error message though, which meant the error should be at the end of the generated log. Â Fortunately, you can search for the error message:

<span style="font-family: 'courier new' , 'courier' , monospace; font-size: x-small;">[2]06B0.2DE4::â€Ž2016â€Ž-â€Ž03â€Ž-â€Ž30 09:41:30.817 [Microsoft-AppV-Client]Package {499ed340-c809-47dc-a533-2cdeab537e93} version {3589c28b-edb7-41d6-865d-e01c4fdd4318} failed configuration in folder 'D:AppVDataPackageInstallationRoot' with error 0x4C40310C-0x12.Â </span>

I suspect the first bit of hexadecimal code (06B0.2DE4) are probably an identifier for a thread or some such so I suspect if I search for just this code I can be shown all events that lead up to this error:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://2.bp.blogspot.com/-JlXexqCrQjk/Vvv2hnmrcKI/AAAAAAAABr0/IfVBzYpvT5ERMvpCYLF1blRC0BQxYg-lg/s1600/11.PNG"><img src="https://2.bp.blogspot.com/-JlXexqCrQjk/Vvv2hnmrcKI/AAAAAAAABr0/IfVBzYpvT5ERMvpCYLF1blRC0BQxYg-lg/s320/11.PNG" width="320" height="117" border="0" /></a>
</div>

Looks like it.

The log up to the error:

<pre class="lang:default decode:true ">[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-Transport]A stream request (handle 0) creation called.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-TransportDataSource]ComAppxRangeRequestJob::Run() - stream content created. RequestHandle: 51.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-Transport]Run request (handle: 51) called.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-Transport]Stream request run with priority 0 started.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-Transport]Request state transitioning to Run.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.207 [Microsoft-AppV-Streaming-Transport]File handle available to service request.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.329 [Microsoft-AppV-Streaming-Transport]Stream request run with priority 0 ended, 0 attempts made.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.329 [Microsoft-AppV-Streaming-Transport]Request (handle 51) succeeded.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.329 [Microsoft-AppV-Streaming-TransportDataSource]ComAppxRangeRequestJob::Run() - Run request succeeded. RequestHandle: 51.
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.329 [Microsoft-AppV-Client-AppxPackaging]Package metadata downloaded
[2]06B0.2DE4::?2016?-?03?-?30 09:40:05.371 [Microsoft-AppV-Client-AppxPackaging]Creating blockmap reader
[4]06B0.2DE4::?2016?-?03?-?30 09:40:05.750 [Microsoft-AppV-Client-AppxPackaging]Blockmap reader created
[2]06B0.2DE4::?2016?-?03?-?30 09:40:06.133 [Microsoft-AppV-Client-AppxPackaging]Verifying the file stream against the block map file hash
[2]06B0.2DE4::?2016?-?03?-?30 09:40:06.815 [Microsoft-AppV-Client-AppxPackaging]Block map file hash verified against the file stream
[2]06B0.2DE4::?2016?-?03?-?30 09:40:06.815 [Microsoft-AppV-Client-AppxPackaging]Creating app manifest reader
[3]06B0.2DE4::?2016?-?03?-?30 09:40:08.327 [Microsoft-AppV-Client-AppxPackaging]App manifest reader created
[1]06B0.2DE4::?2016?-?03?-?30 09:40:09.263 []***
[1]06B0.2DE4::?2016?-?03?-?30 09:40:09.263 [Microsoft-AppV-Client-AppxPackaging]IAppxPackageStreamingReader created
[1]06B0.2DE4::?2016?-?03?-?30 09:40:09.267 [Microsoft-AppV-Streaming-Manager]Microsoft AppV Streaming Manager Session created for URI \\healthy.bewell.ca\apps\AppV\AppV5\PROD\Epic_Hyperspace_2014_8.1_RA1471_CP7_x86\Epic_Hyperspace_2014_8.1_RA1471_CP7_x86.appv and user SID S-1-5-21-38857442-2693285798-3636612711-15138285.
[0]06B0.2DE4::?2016?-?03?-?30 09:41:30.702 [Microsoft-AppV-Streaming-Manager]Failed to parse uri provider guid, result = The configuration registry key is invalid..
[0]06B0.2DE4::?2016?-?03?-?30 09:41:30.703 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.698 - Orchestrator: [1712].[11748]: INFO: ActivityManagerImpl::RequestActivity() - GetPackageGUIDsFromURL activity #404 succeeded.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.714 - Orchestrator: [1712].[11748]: INFO: ActivityManagerImpl::RequestActivity() - Starting activity ConfigurePackage #405.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.714 - Orchestrator: [1712].[11748]: INFO: PendingActivities::Add() - New activity ConfigurePackage #405 for (entity 499ed340-c809-47dc-a533-2cdeab537e93, version 3589c28b-edb7-41d6-865d-e01c4fdd4318) is pending coordination with other running activity instances.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.714 - Orchestrator: [1712].[11748]: INFO: RunningActivities::Add() - New activity ConfigurePackage #405 for (entity 499ed340-c809-47dc-a533-2cdeab537e93, version 3589c28b-edb7-41d6-865d-e01c4fdd4318) has been coordinated with other running activity instances.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.714 - Orchestrator: [1712].[11748]: INFO: PendingActivities::Remove() - New activity ConfigurePackage #405 for (entity 499ed340-c809-47dc-a533-2cdeab537e93, version 3589c28b-edb7-41d6-865d-e01c4fdd4318) is no longer pending coordination with other running activity instances.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-Streaming-Transport]Connect Close called.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.724 [Microsoft-AppV-Streaming-Transport]Cancel all requests called.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-Streaming-Manager]Microsoft AppV Streaming Manager Session deleted for URI \\healthy.bewell.ca\apps\AppV\AppV5\PROD\Epic_Hyperspace_2014_8.1_RA1471_CP7_x86\Epic_Hyperspace_2014_8.1_RA1471_CP7_x86.appv and user SID S-1-5-21-38857442-2693285798-3636612711-15138285.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.807 - Orchestrator: [1712].[11748]: ERROR: ClientActivity::ExecuteActivity() - Activity ConfigurePackage #405 failed on component StreamingManager (error: 1279275276-18).
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.807 - Orchestrator: [1712].[11748]: ERROR: ConfigurePackageActivity::PostComponent() - ConfigurePackage activity #405 was failed by component StreamingManager (error: 0x55900d02-0x501), initiating a ConfigurePackageUndo.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-Client]Package {499ed340-c809-47dc-a533-2cdeab537e93} version {3589c28b-edb7-41d6-865d-e01c4fdd4318} failed configuration in folder 'D:\AppVData\PackageInstallationRoot\' with error 0x4C40310C-0x12.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.807 - Orchestrator: [1712].[11748]: INFO: RunningActivities::Remove() - Activity ConfigurePackage #405 for (entity 499ed340-c809-47dc-a533-2cdeab537e93, version 3589c28b-edb7-41d6-865d-e01c4fdd4318) is no longer coordinated with other running activity instances.
[2]06B0.2DE4::?2016?-?03?-?30 09:41:30.817 [Microsoft-AppV-ServiceLog]2016-Mar-30 09:41:30.807 - Orchestrator: [1712].[11748]: ERROR: ActivityManagerImpl::RequestActivity() - ConfigurePackage activity #405 failed with error code 1435503874-1281.</pre>

With the error time code, I can compare what Procmon says is going on. Â And procmon reports back the following (at exactly 09:41:30.815):

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://1.bp.blogspot.com/-zohqSAx47VM/VvwENAgF0uI/AAAAAAAABsE/GeH54SznqZYutVEjkJcCIucMNDS-p44LA/s1600/13.PNG"><img src="https://1.bp.blogspot.com/-zohqSAx47VM/VvwENAgF0uI/AAAAAAAABsE/GeH54SznqZYutVEjkJcCIucMNDS-p44LA/s320/13.PNG" width="320" height="176" border="0" /></a>
</div>

Procmon has the AppVClient.exe doing the following action "QuerySecurityFile" and it's looking for "Information:Owner". Â So what is this the value of this property?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://1.bp.blogspot.com/-cAzqrv3NJZg/VvwEevq9iwI/AAAAAAAABsI/n9agZnYXgdwBjgk9SRhJsNUL9j6JhQb-Q/s1600/14.PNG"><img src="https://1.bp.blogspot.com/-cAzqrv3NJZg/VvwEevq9iwI/AAAAAAAABsI/n9agZnYXgdwBjgk9SRhJsNUL9j6JhQb-Q/s320/14.PNG" width="320" height="240" border="0" /></a>
</div>

Hmmm... Â This does not look correct to me. Â From my experience, I think the current owner ends up being 'SYSTEM' because that's the account the AppVClient.exe runs under when it creates this folder. Â As a hunch, I looked at a system that is operating correctly and compared it's Owner attribute:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://2.bp.blogspot.com/-MLxVNPa_jhI/VvwEyvh7gMI/AAAAAAAABsM/P1_DX9Qv9tQGNKKUfCtx_b5SH4KI7MOLQ/s1600/15.PNG"><img src="https://2.bp.blogspot.com/-MLxVNPa_jhI/VvwEyvh7gMI/AAAAAAAABsM/P1_DX9Qv9tQGNKKUfCtx_b5SH4KI7MOLQ/s320/15.PNG" width="320" height="215" border="0" /></a>
</div>

Sure enough, on a working system the Owner is SYSTEM. Â So I'll attempt to modify the non-working system and retry adding the package:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="https://4.bp.blogspot.com/-Yx53tiqFKIU/VvwFxi6inMI/AAAAAAAABsY/JLVZQssDskIfp5-CCyvFlx8HRqK8eDwow/s1600/16.PNG"><img src="https://4.bp.blogspot.com/-Yx53tiqFKIU/VvwFxi6inMI/AAAAAAAABsY/JLVZQssDskIfp5-CCyvFlx8HRqK8eDwow/s320/16.PNG" width="320" height="202" border="0" /></a>
</div>

Success! Â So it appears this error "<span style="font-family: 'courier new' , 'courier' , monospace; font-size: x-small;">0x4C40310C-0x12&8243;Â </span>is DIRECTLY related to the ownership attribute on your PackageInstallationRoot folder. Â If it is anything but SYSTEM it looks like it fails. Â I do not know why that attribute changed, but changing it back to SYSTEM resolves this error code.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->