---
id: 2539
title: 'Citrix Receiver 4.9 &#8211; Icons flickering, slow PNA performance, annoyances'
date: 2017-09-21T11:28:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2539
permalink: /?p=2539
categories:
  - Uncategorized
---
[Citrix has a problem with the Windows version of Receiver](https://theorypc.ca/2015/10/28/citrix-receiver-4-rants-and-your-apps-are-not-available-at-this-time-please-try-again-in-a-few-minutes-or-contact-your-help-desk-with-this-information/). Â If you want the features and functionality of Receiver 3.X &#8212; most notable &#8220;PNA&#8221;, which integrates your applications into your desktop, you have probably felt a lot of pain. Â Namely, Citrix has been pushing their new &#8216;model&#8217; for a while; store based, which means making your apps &#8220;generally&#8221; available and having users &#8220;subscribe&#8221; to their app they want. Â This model has proven successful with Apple and their &#8220;AppStore&#8221; and that appears to be what Citrix is aping.

I have seen organizations though, that have applications &#8220;assigned&#8221; to users and then &#8220;filtered&#8221; via user groups. Â This is the model of Web Interface and XenApp 6.5. Â Technically, there is no real issue with either of these models, but Citrix Receiver causes an issue but how it has been changed to operate with the &#8220;Store&#8221; model severly impacts the older-way of doing things (if you migrated forward with this method).

I have a colleague by the name of **Martin Loubser** who did all the leg work on this issue, including the problem statement and resolution.

**Problem:** 

Flashing occurs when using &#8220;Citrix PNA&#8221;. Â This can occur when launching applications and refreshing applications.Â Â Clicking on objects like the start menu will cause them to &#8220;close&#8221; as the flashing occurs, making it difficult or nearly impossible to operate. Â If you have lots of published icons this process can take an extremely long time. Â During this time you may be able to see the Citrix icons populate and/or the modification date of the icons will change.

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2539-5" width="1140" height="713" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/09/CitrixReceiverIconFlash.mp4?_=5" /><a href="http://theorypc.ca/wp-content/uploads/2017/09/CitrixReceiverIconFlash.mp4">http://theorypc.ca/wp-content/uploads/2017/09/CitrixReceiverIconFlash.mp4</a></video>
</div>

&nbsp;

Cause:

The root cause for this issue appears be Receiver&#8217;s icon creation. Â Receiver, as it generates an icon it generates a &#8220;thumbprint&#8221; that appears to associate the application with that icon. Â In order to verify this is your situation, you&#8217;ll need to [enable verbose logging on Receiver](https://support.citrix.com/article/CTX132883).

Once verbose logging is enabled, refresh (or reset) Receiver and browse this file:

&#8220;%userprofile%\AppData\Local\Citrix\SelfService\SelfService.txt&#8221;

Specifically, you&#8217;ll look for something like &#8220;The thumbprint is still null&#8221;:

&nbsp;

<pre class="lang:default decode:true">GetDetails from Network for pnagentspe-c4bd576e@@AHSCTX:LabGuide Icon32, RawIcon32, RawIcon48
21/11:20:33 AM pna      &gt; T108    :      {
21/11:20:33 AM pna      = T108    :        DoGetDetails r=PNAgentClient(pnagentspe-c4bd576e) details=Icon32, RawIcon32, RawIcon48
21/11:20:33 AM pna      &gt; T108    :        {
21/11:20:33 AM pna        T108    :          Do http request
21/11:20:33 AM pna        T108    :          PNA SSOn functionality being used. Suppressing credential request from user.
21/11:20:33 AM authman  ? T108    :          Creating AuthManager request context to "https://bottheory.local/Citrix/PNAgentSPE/enum.aspx" under name "PNA" with flags "None"...
21/11:20:33 AM authman  ? T108    :          Prepared AuthManager request context with result: Continue
21/11:20:33 AM authman  ? T108    :          Got translated url from AuthManager: https://bottheory.local/Citrix/PNAgentSPE/enum.aspx
21/11:20:33 AM pna        T108    :          Adding sson logon method to request.
21/11:20:33 AM pna        T108    :          SSOMode = CredsFromAuthMan
21/11:20:33 AM pna        T108    :          PNA SSon functionlity enabled. Sending request to AuthManager.
21/11:20:33 AM pna      = T108    :           call SendAndReceiveRequestViaAuthman: https://bottheory.local/Citrix/PNAgentSPE/enum.aspx
21/11:20:33 AM pna      &gt; T108    :          {
21/11:20:33 AM authman    T108    :            LazyInitAuthManagerConnection, IncreaseUsage Usage = 3
21/11:20:34 AM authman    T107    :            LazyInitAuthManagerConnection, DecreaseUsage Usage = 2
21/11:20:34 AM pna      ? T107    :          }00:00:00.3110000
21/11:20:34 AM authman    T107    :          Disposing AuthManager request context...
21/11:20:34 AM pna      ? T107    :        }00:00:00.3640000
21/11:20:34 AM pna        T107    :        Start XML processing
21/11:20:34 AM pna      = T107    :        Process XML for applications
21/11:20:34 AM pna      &gt; T107    :        {
21/11:20:34 AM pna        T107    :          PNAResource.SetIcon: The thumbprint is still null, set thumbprint for Epic to 68765976449
21/11:20:34 AM pna      ? T107    :        }00:00:00.0030000
21/11:20:34 AM pna      ? T107    :      }00:00:00.3670000
21/11:20:34 AM misc     ? T107    :    }00:00:00.3670000</pre>

Notice this line: &#8220;PNAResource.SetIcon: The thumbprint is still null, set thumbprint for Epic to 68765976449&#8221;

The thumbprint should have a matching file in %userprofile%\AppData\Local\Citrix\SelfService\img. Â In my example, my img folder contains the following files:

<img class="aligncenter size-full wp-image-2541" src="http://theorypc.ca/wp-content/uploads/2017/09/BadRecevier.png" alt="" width="896" height="594" srcset="http://theorypc.ca/wp-content/uploads/2017/09/BadRecevier.png 896w, http://theorypc.ca/wp-content/uploads/2017/09/BadRecevier-300x199.png 300w, http://theorypc.ca/wp-content/uploads/2017/09/BadRecevier-768x509.png 768w" sizes="(max-width: 896px) 100vw, 896px" /> 

Notice there is no &#8220;68765976449-Icon&#8221; files in my list.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->