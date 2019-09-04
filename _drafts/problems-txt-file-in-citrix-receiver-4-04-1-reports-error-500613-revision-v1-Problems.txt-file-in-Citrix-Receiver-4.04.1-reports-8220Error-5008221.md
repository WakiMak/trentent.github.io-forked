---
id: 1068
title: 'Problems.txt file in Citrix Receiver 4.0/4.1 reports &#8220;Error 500&#8221;'
date: 2016-04-24T22:49:22-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/613-revision-v1/
permalink: /2016/04/24/613-revision-v1/
---
<pre class="lang:default decode:true ">Tuesday, April 22, 2014/1:13:53 AM Launch raw="-launch" "-s" "pnagent-8c72c230" "-CitrixID" "pnagent-8c72c230@@compFARM01:ORANGE Desktop" "-ica" "compFARM01:ORANGE Desktop" "-cmdline", state=Dazzle.AppMonitor.MainState silent=False res=pnagent-8c72c230@@compFARM01:ORANGE Desktop
Tuesday, April 22, 2014/1:13:53 AM Try launch type 0 of 1 = IcaLaunchInfo
Tuesday, April 22, 2014/1:13:53 AM Get ica file for 'pnagent-8c72c230@@compFARM01:ORANGE Desktop'
Tuesday, April 22, 2014/1:13:53 AM Creating AuthManager request context to "http://myappscomp.ca/Citrix/PNAgent/launch.aspx" under name "PNA" with flags "None"...
Tuesday, April 22, 2014/1:13:53 AM Prepared AuthManager request context with result: Continue
Tuesday, April 22, 2014/1:13:53 AM Got translated url from AuthManager: http://myappscomp.ca/Citrix/PNAgent/launch.aspx
Tuesday, April 22, 2014/1:13:54 AM GetICAFileRetryLoop: retrying due to possible bogus ResourceUnavailable error
Tuesday, April 22, 2014/1:13:54 AM GetICAFileRetryLoop: retrying in 00:00:02
Tuesday, April 22, 2014/1:13:56 AM Creating AuthManager request context to "http://myappscomp.ca/Citrix/PNAgent/launch.aspx" under name "PNA" with flags "None"...
Tuesday, April 22, 2014/1:13:56 AM Prepared AuthManager request context with result: Continue
Tuesday, April 22, 2014/1:13:56 AM Got translated url from AuthManager: http://myappscomp.ca/Citrix/PNAgent/launch.aspx
Tuesday, April 22, 2014/1:13:56 AM GetICAFileRetryLoop: retrying due to possible bogus ResourceUnavailable error
Tuesday, April 22, 2014/1:13:56 AM GetICAFileRetryLoop: retrying in 00:00:02
Tuesday, April 22, 2014/1:13:58 AM Creating AuthManager request context to "http://myappscomp.ca/Citrix/PNAgent/launch.aspx" under name "PNA" with flags "None"...
Tuesday, April 22, 2014/1:13:58 AM Prepared AuthManager request context with result: Continue
Tuesday, April 22, 2014/1:13:58 AM Got translated url from AuthManager: http://myappscomp.ca/Citrix/PNAgent/launch.aspx
Tuesday, April 22, 2014/1:13:59 AM GetICAFileRetryLoop: retrying due to possible bogus ResourceUnavailable error
Tuesday, April 22, 2014/1:13:59 AM GetICAFileRetryLoop: retrying in 00:00:02
Tuesday, April 22, 2014/1:14:01 AM Creating AuthManager request context to "http://myappscomp.ca/Citrix/PNAgent/launch.aspx" under name "PNA" with flags "None"...
Tuesday, April 22, 2014/1:14:01 AM Prepared AuthManager request context with result: Continue
Tuesday, April 22, 2014/1:14:01 AM Got translated url from AuthManager: http://myappscomp.ca/Citrix/PNAgent/launch.aspx
Tuesday, April 22, 2014/1:14:01 AM GetICAFileRetryLoop: retrying due to possible bogus ResourceUnavailable error
Tuesday, April 22, 2014/1:14:01 AM GetICAFileRetryLoop: retrying in 00:00:02
Tuesday, April 22, 2014/1:14:03 AM Creating AuthManager request context to "http://myappscomp.ca/Citrix/PNAgent/launch.aspx" under name "PNA" with flags "None"...
Tuesday, April 22, 2014/1:14:03 AM Prepared AuthManager request context with result: Continue
Tuesday, April 22, 2014/1:14:03 AM Got translated url from AuthManager: http://myappscomp.ca/Citrix/PNAgent/launch.aspx
Tuesday, April 22, 2014/1:14:04 AM Got Comms Error
Tuesday, April 22, 2014/1:14:04 AM The remote server returned an error: (500) Internal Server Error.
Tuesday, April 22, 2014/1:14:04 AM    at Dazzle.PNAgent.PNAgentClient.DoRequestOnce(Uri iurl, Uri nurl, String request, String requestB, Boolean isRequestForPreLaunch)
Tuesday, April 22, 2014/1:14:04 AM    at Dazzle.PNAgent.PNAgentClient.DoRequest(Uri iurl, Uri nurl, String request, String requestB, Boolean allowCredUI, Boolean isRequestForPreLaunch)
Tuesday, April 22, 2014/1:14:04 AM    at Dazzle.PNAgent.PNAgentClient.Dazzle.Model.IICAProvider.GetICAFile(String InName, String retryKey, Boolean allowCredUI, String& url, Boolean isRequestForPreLaunch)
Tuesday, April 22, 2014/1:14:04 AM    at Dazzle.Launcher.ICARunner.GetICAFileRetryLoop(IICAProvider icastore, String resourceId, LaunchProgressUI launchProgressUI, String& url, Boolean isRequestForPreLaunch)
Tuesday, April 22, 2014/1:14:04 AM    at Dazzle.Launcher.ICARunner.Launch(IProvider store, IGlobalState state, String resourceId, String citrixId, String cmdline, ReconnectAction icaReconnectAction, Boolean isRequestForPreLaunch)</pre>

Make sure you go to the PNA agent web folder on the designated server and setÂ <span style="background-color: white; color: #4d4f53; font-family: HelveticaNeueW01-55Roma, Helvetica, Arial, sans-serif; font-size: 13px;">RequireLaunchReference=OffÂ </span>and remove the # in the WebInterface.conf file.

http://support.citrix.com/article/CTX123003

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->