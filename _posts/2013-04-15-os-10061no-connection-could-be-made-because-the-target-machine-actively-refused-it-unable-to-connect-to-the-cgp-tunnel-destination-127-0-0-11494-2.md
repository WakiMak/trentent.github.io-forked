---
id: 654
title: '(OS 10061)No connection could be made because the target machine actively refused it.  : Unable to connect to the CGP tunnel destination (127.0.0.1:1494)'
date: 2013-04-15T16:22:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/04/15/os-10061no-connection-could-be-made-because-the-target-machine-actively-refused-it-unable-to-connect-to-the-cgp-tunnel-destination-127-0-0-11494-2/
permalink: /2013/04/15/os-10061no-connection-could-be-made-because-the-target-machine-actively-refused-it-unable-to-connect-to-the-cgp-tunnel-destination-127-0-0-11494-2/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/04/os-10061no-connection-could-be-made.html
blogger_internal:
  - /feeds/7977773/posts/default/2690967925154612164
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - &
---
(OS 10061)No connection could be made because the target machine actively refused it.Â : Unable to connect to the CGP tunnel destination (127.0.0.1:1494)

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-cWx9C5h-dVw/UWx_JOjhW6I/AAAAAAAAANM/Z2HG3k8jOMI/s1600/3.jpg"><img src="http://2.bp.blogspot.com/-cWx9C5h-dVw/UWx_JOjhW6I/AAAAAAAAANM/Z2HG3k8jOMI/s320/3.jpg" width="320" height="222" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      We got this error on a & 6.5 provisioning server
    </td>
  </tr>
</table>

We utilize Citrix PVS for our & servers.Â After updating our & vDisk using our usual method and rebooting the server we got the above error.Â Since we versioned it we had our usual servers working without issue, and a couple of "test" versions failing.Â I started troubleshooting this using the google method of shotgun trying the first 5 error/solutions.Â This included, disabling re-enabling ICA listener, setting the listener on only one NIC interface (which it was, I toggled it for all then just one), deleting the ICA listener and recreating it.Â None of them worked so I started troubleshooting.

I encountered this error today and started troubleshooting.Â The first step I did was trace the path to the error using Procmon.exe.Â This, unfortunately, did not reveal anything of substance.Â What I found with this error is the network process is almost exactly the same for both.Â I can see XTE.exe communicating via 2598, I can see, what appears to be it handing it off to IMASrv.exe.Â At this point is when it causes that error message to be displayed on the bad machine, but IMASrv.exe continues on the good machine.

At this point I'm thinking that maybe the IMA settings for session reliability have become corrupted.Â It was suggested to me to disable session reliability and see if it works by a colleague of mine.

The odd thing about resetting the IMA policy is that it isn't even set in the first place.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-Bt_Dgyu2QiY/UWx5rFh9WRI/AAAAAAAAAM8/ry0sSb15154/s1600/1.jpg"><img src="http://3.bp.blogspot.com/-Bt_Dgyu2QiY/UWx5rFh9WRI/AAAAAAAAAM8/ry0sSb15154/s320/1.jpg" width="320" height="122" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      "Add" means it is not set
    </td>
  </tr>
</table>

But I disabled it anyways in the IMA policy in AppCenter, NOT in group policy.Â After setting session reliability to disabled I restarted the IMA Service on the server and the XTE service.Â The XTE service refused to start, I assume because we disabled it, and the IMA service came up.Â I then connected to the application via 1494 without issue.Â At this point I removed the session reliability setting again.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-EjQIo6FYKGY/UWx6gsOmaSI/AAAAAAAAANE/gO4aRpbNIEI/s1600/2.jpg"><img src="http://2.bp.blogspot.com/-EjQIo6FYKGY/UWx6gsOmaSI/AAAAAAAAANE/gO4aRpbNIEI/s320/2.jpg" width="320" height="128" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Click "Remove"
    </td>
  </tr>
</table>

And then waited a minute or so and restarted the IMA Service and XTE Service.Â The XTE service came up cleanly, running netstat -a only showed 2598 was listening and I retried the application hosted on the troublesome server.

BANG, it worked like a charm.Â I wish I knew why it was happening with more detail, unfortunately, this is as far as I got and I cannot make the error occur again.

The next time this occurs I would like to try stopping the IMA Service and removing this file:  
C:\Program Files (x86)\Citrix\FarmGpo\FarmGpo.bin

I think that contains the local IMA policy that may have been corrupted and causing my issue.

=======================EDIT===================

[I believe I have fixed the issue here. Â It appears it may be a NIC binding issue.](http://trentent.blogspot.ca/2013/05/os-10061no-connection-could-be-made.html)

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->