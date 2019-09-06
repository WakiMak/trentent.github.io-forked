---
id: 637
title: Troubleshooting Audio issues in Citrix &
date: 2013-07-10T14:04:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/07/10/troubleshooting-audio-issues-in-citrix-&/
permalink: /2013/07/10/troubleshooting-audio-issues-in-citrix-&/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/07/troubleshooting-audio-issues-in-citrix.html
blogger_internal:
  - /feeds/7977773/posts/default/2118451160913618718
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Group Policy

---
We recently ran across an issue with & 6.5 where we were publishing an application that required the "Beep" but it wasn't working. Â The following is the troubleshooting steps I did to enable audio to work on that application.

First we created a Citrix policy to enable audio. Â This policy looked like so:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-ijXkPZ8_9kQ/Ud23SNL7asI/AAAAAAAAAVs/WSnoILAPw3E/s1600/1.PNG"><img src="http://3.bp.blogspot.com/-ijXkPZ8_9kQ/Ud23SNL7asI/AAAAAAAAAVs/WSnoILAPw3E/s320/1.PNG" width="320" height="125" border="0" /></a>
</div>

We filtered on a user security group to enable the client audio redirection and added that filter group to the application. Â From the original appearance of things, this should have been sufficient to enable client redirection. Â But it did not. Â So I wanted to verify that the policy was actually applying to the user account. Â To do that, you login to the system with a user account and check in Regedit for the value "AllowAudioRedirection". Â If it's set to 0x1 then the Citrix group policy has evaluated that client redirection should be enabled for your session.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-FbTF1gqcU5w/Ud24_PBFgqI/AAAAAAAAAWA/HxfJqTnfc6A/s1600/2.PNG"><img src="http://3.bp.blogspot.com/-FbTF1gqcU5w/Ud24_PBFgqI/AAAAAAAAAWA/HxfJqTnfc6A/s320/2.PNG" width="320" height="184" border="0" /></a>
</div>

Unfortunately, I still did not have audio redirection working...

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-XWHnYAVhaC8/Ud24-7BRLNI/AAAAAAAAAWE/WsVhgNbkQKs/s1600/3.PNG"><img src="http://2.bp.blogspot.com/-XWHnYAVhaC8/Ud24-7BRLNI/AAAAAAAAAWE/WsVhgNbkQKs/s320/3.PNG" width="320" height="258" border="0" /></a>
</div>

Citrix advises that you can use dbgview.exe to further troubleshoot the Citrix Receiver to assist. Â I launched dbgview.exe and started the trace and launched the application from the Webinterface.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Vbalo16fqYs/Ud27kPW6jeI/AAAAAAAAAWU/ZJh8j3RZENc/s1600/4.PNG"><img src="http://4.bp.blogspot.com/-Vbalo16fqYs/Ud27kPW6jeI/AAAAAAAAAWU/ZJh8j3RZENc/s320/4.PNG" width="320" height="234" border="0" /></a>
</div>

"00000043 0.60113800 [7752] CAMSetAudioSecurity: Wd_FindVdByName failed"

CAM is a virtual channel (<span style="background-color: white; color: #35383d; font-family: Arial, Helvetica, sans-serif; font-size: 12px;">Virtual Channel Priority for Audio</span>) and we can see it's failing. Â I then used the Citrix ICA creator and launched the application using that. Â The dbgview for that output looks like so:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-Q87IRC1QpwM/Ud274KA30oI/AAAAAAAAAWc/E54UMVL-Jn4/s1600/5.PNG"><img src="http://3.bp.blogspot.com/-Q87IRC1QpwM/Ud274KA30oI/AAAAAAAAAWc/E54UMVL-Jn4/s320/5.PNG" width="320" height="239" border="0" /></a>
</div>

00000013 0.44386363 [4496] CAMSetAudioSecurity: success

We can see that the audio virtual channel was able to successfully latch and I confirmed I had audio in the application.

From here the issue appeared to be when I launched the application from the webinterface or desktop shortcut. Â I then compared the two ICA files, the one from the web interface and the one I created separately to see what was different. Â The difference was glaringly obvious. Â The working ICA file had "ClientAudio=On" and the broken one had "ClientAudio=Off".

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-uY7Bbzmc1Eg/Ud28i64SY_I/AAAAAAAAAWk/loKKKn6Zyk4/s1600/6.PNG"><img src="http://4.bp.blogspot.com/-uY7Bbzmc1Eg/Ud28i64SY_I/AAAAAAAAAWk/loKKKn6Zyk4/s320/6.PNG" width="320" height="127" border="0" /></a>
</div>

Curious, I launched AppCenter and clicked through the applications settings and saw the following:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-eyQthmmjBNM/Ud29Q6yygkI/AAAAAAAAAWs/b_44DuAf1vQ/s1600/7.PNG"><img src="http://4.bp.blogspot.com/-eyQthmmjBNM/Ud29Q6yygkI/AAAAAAAAAWs/b_44DuAf1vQ/s320/7.PNG" width="320" height="207" border="0" /></a>
</div>

"Enable legacy audio" was unchecked. Â I checked it and then logged off and logged back on the web interface and when I downloaded the ICA file, "ClientAudio=On" and I had audio. Â I then unchecked that setting and confirmed it manipulated the ICA file as with it unchecked the ICA file generated had "ClientAudio=Off"

Who knows why it's called "legacy audio". Â May as well just call that option "Enable audio" as that would be more accurate. Â The Citrix documents on this setting says the following:

<http://support.citrix.com/proddocs/topic/&6-w2k8-admin/ps-sessions-en-dis-aud-pubapp-v2.html>  
To enable or disable audio for published applications

<div>
  <span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;">If you <b><u>disable audio for a published application, audio is not available within the application under any condition</u></b>. If you enable <b><u>audio for an application, you can use policy settings and filters to further define under what conditions audio</u></b> is available within the application.</span></p> 
  
  <ol>
    <li>
      <span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;">In the Delivery Services Console, select the published application for which you want to enable or disable audio, and select Action > Application properties.Â </span>
    </li>
    <li>
      <span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;">In the Application Properties dialog box, click Advanced > Client options. Select or clear the Enable legacy audio check box.</span>
    </li>
  </ol>
  
  <p>
    Emphasis is mine.
  </p>
  
  <p>
    Anyways, and now we have our applications with working audio and everything seems to be good again ðŸ™‚
  </p>
  
  <p>
    To summarize the enable audio for a & application you must:<br /> 1) Enable "legacy" audio<br /> 2) Enable a Citrix policy to configure audio redirection<br /> 3) Done.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->