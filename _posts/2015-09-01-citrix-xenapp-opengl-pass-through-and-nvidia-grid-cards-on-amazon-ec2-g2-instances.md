---
id: 551
title: Citrix &, OpenGL pass-through and Nvidia GRID cards on Amazon EC2 (G2 Instances)
date: 2015-09-01T15:55:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/01/citrix-&-opengl-pass-through-and-nvidia-grid-cards-on-amazon-ec2-g2-instances/
permalink: /2015/09/01/citrix-&-opengl-pass-through-and-nvidia-grid-cards-on-amazon-ec2-g2-instances/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/citrix-&-opengl-pass-through-and.html
blogger_internal:
  - /feeds/7977773/posts/default/267529103177304288
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance

  - XenDesktop
---
I'm attempting to do a Proof of Concept (POC) for a client and one of the ideas was to utilize the Amazon EC2 cloud to provide GPU instances to the users for their applications (Maya, SolidWorks, etc.).  In order to understand how GPU sharing works, I setup my home lab to take advantage of these features first, in order to understand how it operates.

[Citrix provides documentation on setting up the GPU sharing](http://support.citrix.com/proddocs/topic/&-xendesktop-76/xad-hdx3dpro-gpu-accel-server.html).  For my test, I'm doing this on a bare metal Citrix server.  Essentially, the notes state that OpenGL is automatically shared and enabled and special steps must be taken for DirectX, OpenCL, CUDA and Windows Server 2012.  To enable GPU sharing for & for these features, the following registry file will enable these:

> <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Windows Registry Editor Version 5.00<br /> Windows Registry Editor Version 5.00<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "DirectX"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "DirectX"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\CtxHook\AppInit_Dlls\Multiple Monitor Hook]<br /> "EnableWPFHook"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Multiple Monitor Hook]<br /> "EnableWPFHook"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "CUDA"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "CUDA"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "OpenCL"=dword:00000001<br /> [HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Graphics Helper]<br /> "OpenCL"=dword:00000001 </span>

In addition to this registry file, for Server 2012, the following Group Policy object is required:

<ul style="box-sizing: border-box; font-family: Arial, Helvetica, sans-serif; font-size: 12px; line-height: 17px; margin-bottom: 2em; margin-top: 12px !important;">
  <li style="box-sizing: border-box; font-family: Verdana, Arial, 'Sans Serif' !important; font-size: 9pt !important; margin-bottom: 12px !important; margin-top: 0px !important;">
    On Windows Server 2012, Remote Desktop Services (RDS) sessions on the RD Session Host server use the Microsoft Basic Render Driver as the default adapter. To use the GPU in RDS sessions on Windows Server 2012, enable the <b>Use the hardware default graphics adapter for all Remote Desktop Services sessions</b> setting in the group policy Local Computer Policy > Computer Configuration > Administrative Templates > Windows Components > Remote Desktop Services > Remote Desktop Session Host > Remote Session Environment.
  </li>
</ul>

My initial setup is a Q87M-E system with a Intel 4771 and onboard graphics.  My system is setup with Windows 2012 R2 with Citrix & 7.6.

Launching an ICA session to the & 7.6 server results in:

<div style="clear: both; text-align: center;">
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-sbALIHG4uE4/VdeOtlxWklI/AAAAAAAABCE/sjCFoJNrLNc/s1600/Working_Onboard_Intel_HD46002.PNG"><img src="http://1.bp.blogspot.com/-sbALIHG4uE4/VdeOtlxWklI/AAAAAAAABCE/sjCFoJNrLNc/s640/Working_Onboard_Intel_HD46002.PNG" width="640" height="307" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-eoMOijOSwpU/VdeOtqg2NSI/AAAAAAAABCA/J-W1dlfad2U/s1600/Working_Onboard_Intel_HD4600.PNG"><img src="http://2.bp.blogspot.com/-eoMOijOSwpU/VdeOtqg2NSI/AAAAAAAABCA/J-W1dlfad2U/s640/Working_Onboard_Intel_HD4600.PNG" width="640" height="307" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

We have OpenGL working, DirectX 11, and OpenCL (the onboard Intel GPU's do not support CUDA).  So we have a full, working implementation of GPU sharing in a ICA session on a & server.

But the onboard Intel graphics card will not get the me the performance I want.  I had a Nvidia GTX 670 video card on hand to see if I can get better 3D performance.  I installed that card in the system, installed the video drivers and checked the results.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-_zO6_PYxacI/VeSdtn4R8eI/AAAAAAAABCc/0Zg8e32eeD4/s1600/GTX670_ICA_Info1.PNG"><img src="http://3.bp.blogspot.com/-_zO6_PYxacI/VeSdtn4R8eI/AAAAAAAABCc/0Zg8e32eeD4/s640/GTX670_ICA_Info1.PNG" width="640" height="298" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-h6HdlnA8OR4/VeSdufJ4GiI/AAAAAAAABCk/AsEu-4Jy2n4/s1600/GTX670_ICA_Info2.PNG"><img src="http://4.bp.blogspot.com/-h6HdlnA8OR4/VeSdufJ4GiI/AAAAAAAABCk/AsEu-4Jy2n4/s640/GTX670_ICA_Info2.PNG" width="640" height="298" border="0" /></a>
</div>

Where did my OpenGL go?  Everything else is working correctly; Direct3D, CUDA, Open<u>C</u>L, but not OpenGL.  My understanding from Nvidia is that OpenGL should just be 'passed through' by Citrix.  I know that it \*does\* pass-through because we, literally, just saw it with the onboard Intel GPU and the Intel drivers.

My next thought is maybe it had to do with the drivers?  Maybe if I tried the Quadro drivers?  It turns out Nvidia has released special [Quadro drivers that enable OpenGL in a RDP session](http://www.nvidia.com/download/driverResults.aspx/78297/en-us). Maybe if I modified the INF to add my GTX670 to these special drivers I could get OpenGL to work?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-eYtQy9v2MZk/VeSgjbikRZI/AAAAAAAABC0/R8UIfuu6Q24/s1600/GTX670_QuadroDrivers.PNG"><img src="http://2.bp.blogspot.com/-eYtQy9v2MZk/VeSgjbikRZI/AAAAAAAABC0/R8UIfuu6Q24/s640/GTX670_QuadroDrivers.PNG" width="640" height="304" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-JvqOMbrM260/VeSgjcSVwmI/AAAAAAAABCw/WaosePuaMp8/s1600/GTX670_QuadroDrivers_2.PNG"><img src="http://2.bp.blogspot.com/-JvqOMbrM260/VeSgjcSVwmI/AAAAAAAABCw/WaosePuaMp8/s640/GTX670_QuadroDrivers_2.PNG" width="640" height="298" border="0" /></a>
</div>

It did not work.  OpenGL remained disabled in RDP/ICA sessions.

Suspecting Nvidia is doing some form of detection that is disabling OpenGL (it's probably considered a 'pro'-feature) I acquired a Quadro FX5800 and using the \*same\* modified Quadro drivers, these were my results:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-z8Aa0toQUgo/VeTFRMZ0ZfI/AAAAAAAABDM/_dnJYilroi8/s1600/FX5800_QuadroDrivers.PNG"><img src="http://2.bp.blogspot.com/-z8Aa0toQUgo/VeTFRMZ0ZfI/AAAAAAAABDM/_dnJYilroi8/s640/FX5800_QuadroDrivers.PNG" width="640" height="238" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-v5RANUeW1_0/VeTFRBjmFdI/AAAAAAAABDI/6ocpUKg3-rw/s1600/FX5800_QuadroDrivers2.PNG"><img src="http://1.bp.blogspot.com/-v5RANUeW1_0/VeTFRBjmFdI/AAAAAAAABDI/6ocpUKg3-rw/s640/FX5800_QuadroDrivers2.PNG" width="640" height="238" border="0" /></a>
</div>

OpenGL is now working!!

Ok, so, at this point I know how to enable GPU sharing for Citrix &, I know how to check and verify it's functionality, and I know that different Nvidia cards can have OpenGL enabled or disabled but am not sure if it's the driver that matters or the hardware.  If it's the hardware I'm a bit surprised Intel would incorporate hardware accelerated OpenGL into ICA sessions for their consumer pieces but Nvidia would not for their discrete cards.  To \*attempt\* to test this I went and got the oldest driver I could find that would support a FX5800:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-Sd7BNnSId7M/VeTNci95HlI/AAAAAAAABDg/MruHqOpoCQU/s1600/working_old_fx5800.PNG"><img src="http://1.bp.blogspot.com/-Sd7BNnSId7M/VeTNci95HlI/AAAAAAAABDg/MruHqOpoCQU/s640/working_old_fx5800.PNG" width="640" height="238" border="0" /></a>
</div>

Sure enough, it works.

My last thought is maybe Nvidia has it hard coded somewhere to check for a string or a specific 'type' of video card and, if found, enable OpenGL?

My thinking is that the Nvidia drivers are doing some kind of detection and making a determination between a console session and all others.  If I'm lucky, maybe they only implemented this in their \*newer\* drivers, maybe after they started the RDS OpenGL acceleration...

To test this theory I went and grabbed the oldest driver I could find for my GTX 670 that would work on Windows 2012R2.  [327.23](http://www.nvidia.com/object/win8-win7-winvista-64bit-327.23-whql-driver.html).

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-h1_q2RlICDE/VeYNmTw0CUI/AAAAAAAABD0/4uWDz09aUmI/s1600/GTX670_ICA_Working2.PNG"><img src="http://3.bp.blogspot.com/-h1_q2RlICDE/VeYNmTw0CUI/AAAAAAAABD0/4uWDz09aUmI/s640/GTX670_ICA_Working2.PNG" width="640" height="294" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-ZNAY2NO6iJ8/VeYNmdaa7mI/AAAAAAAABD4/m1mXGKGHxXs/s1600/GTX670_ICA_Working.PNG"><img src="http://4.bp.blogspot.com/-ZNAY2NO6iJ8/VeYNmdaa7mI/AAAAAAAABD4/m1mXGKGHxXs/s640/GTX670_ICA_Working.PNG" width="640" height="294" border="0" /></a>
</div>

Well now...  OpenGL is working.  This is interesting.  And leads evidence that OpenGL is being disabled in ICA via the driver.  I attempted to find when OpenGL \*stopped\* working.

[331.82](http://www.nvidia.com/download/driverResults.aspx/70184/en-us) -> Works, and now with OpenGL 4.4

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-dkuv3zzsz3g/VeYQwm8uHwI/AAAAAAAABEI/eNHlddtMMMY/s1600/GTX670_ICA_Working331.82.PNG"><img src="http://1.bp.blogspot.com/-dkuv3zzsz3g/VeYQwm8uHwI/AAAAAAAABEI/eNHlddtMMMY/s640/GTX670_ICA_Working331.82.PNG" width="640" height="304" border="0" /></a>
</div>

[337.88](http://www.nvidia.com/download/driverresults.aspx/75991/en-us) -> Works

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-Ra8xW8qeQLk/VeYSGg_vaeI/AAAAAAAABEU/1vbk6z6sR_Y/s1600/GTX670_ICA_Working337.88.PNG"><img src="http://4.bp.blogspot.com/-Ra8xW8qeQLk/VeYSGg_vaeI/AAAAAAAABEU/1vbk6z6sR_Y/s640/GTX670_ICA_Working337.88.PNG" width="640" height="304" border="0" /></a>
</div>

[340.52](http://www.nvidia.com/download/driverresults.aspx/77224/en-us) -> No OpenGL.  This driver ([340.52](http://www.nvidia.com/download/driverresults.aspx/77224/en-us)) is now the first gaming driver \*After\* the "OpenGL on RDS release" ([340.43](http://www.nvidia.com/download/driverResults.aspx/76507/en-us)).  It appears something on or after the 340.XX branch is disabling OpenGL in ICA sessions.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-JGcsTKTLNOY/VeYVGjopkmI/AAAAAAAABEg/StMab4luh1Y/s1600/GTX670_ICA_NotWorking340.52.PNG"><img src="http://1.bp.blogspot.com/-JGcsTKTLNOY/VeYVGjopkmI/AAAAAAAABEg/StMab4luh1Y/s640/GTX670_ICA_NotWorking340.52.PNG" width="640" height="298" border="0" /></a>
</div>

At the same time I was testing my Nvidia gaming GPU on my home lab, I was testing Amazon.  The GPU instance that Amazon provides utilize the Nvidia GRID K520 card as a vGPU.  This card is marketed as a '[GRID Gaming](http://www.nvidia.ca/object/cloud-gaming-gpu-boards.html)' card.  I setup this instance with Citrix & and, at the time, used the latest driver ([347.70](http://www.nvidia.com/download/driverResults.aspx/87194/en-us)).  At the time of this testing, this was my 3rd rebuild of this instance so I went with Server 2008 because my previous 2 builds were 2012 and I was convinced I was doing something wrong.  The OS shouldn't matter, but I'm noting it here.

[347.70](http://www.nvidia.com/download/driverResults.aspx/87194/en-us) -> No OpenGL (just like the gaming card):

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-OC7sByPzYpY/VeYYE44I05I/AAAAAAAABEs/dGeu4Qam8Yk/s1600/K520_347.70_Fail.PNG"><img src="http://4.bp.blogspot.com/-OC7sByPzYpY/VeYYE44I05I/AAAAAAAABEs/dGeu4Qam8Yk/s640/K520_347.70_Fail.PNG" width="640" height="280" border="0" /></a>
</div>

Knowing that downgrading the gaming card's driver worked, I installed the oldest driver I could for the K520:

[320.59](http://www.nvidia.com/object/grid-win8-win7-winserv2008r2-winserv2012-64bit-320.59-driver.html) -> OpenGL Works!

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-fHA7VR7SqH4/VeYasnEe4EI/AAAAAAAABE4/KL3ngi5W1pU/s1600/K520_320.59_Working.PNG"><img src="http://1.bp.blogspot.com/-fHA7VR7SqH4/VeYasnEe4EI/AAAAAAAABE4/KL3ngi5W1pU/s640/K520_320.59_Working.PNG" width="640" height="278" border="0" /></a>
</div>

Just like the gaming card.  I suspect the K520 will have the same issue as the GTX 670, and that any driver after 340.XX will disable OpenGL in a ICA session.  Unfortunately, the Grid K520 appears to only have 3 drivers to chose from, 320.59, 335.35, and 347.70.  To finish this testing I will test with [335.35](http://www.nvidia.com/download/driverResults.aspx/74642/en-us):

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-cN596DOsw1I/VeYey5Omy0I/AAAAAAAABFE/G0urvS0q0tM/s1600/K520_ICA_Working335.35.PNG"><img src="http://3.bp.blogspot.com/-cN596DOsw1I/VeYey5Omy0I/AAAAAAAABFE/G0urvS0q0tM/s640/K520_ICA_Working335.35.PNG" width="640" height="272" border="0" /></a>
</div>

OpenGL Works!  So it appears driver 340 and newer will disable OpenGL for ICA sessions across various types of Nvidia GPU's, but not Quadro's..

If you want OpenGL to work on Amazon EC2 instances, you must (at the time of this writing...  hopefully Nvidia corrects this over sight for all cards - consumer and not) you must use a driver older than 340.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->