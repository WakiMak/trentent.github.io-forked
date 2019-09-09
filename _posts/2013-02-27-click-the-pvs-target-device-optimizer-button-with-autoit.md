---
id: 658
title: Click the PVS Target Device Optimizer button with AutoIt
date: 2013-02-27T11:26:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/02/27/click-the-pvs-target-device-optimizer-button-with-autoit/
permalink: /2013/02/27/click-the-pvs-target-device-optimizer-button-with-autoit/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/02/click-pvs-target-device-optimizer.html
blogger_internal:
  - /feeds/7977773/posts/default/4690566928223981063
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - scripting
---
Short but sweet script. Disappointing that Citrix doesn't have a silent run switch for this application.

<pre class="lang:autoit decode:true ">;
; AutoIt Version: 3.0
; Language:       English
; Platform:       Win9x/NT
; Author:         Trentent Tye (trentent@ca.ibm.com)
;
; Script Function:
;   Execute the OK button on the Provisioning Services Device Optimization Tool program
;

; Wait for the Provisioning Services Device Optimization Tool to become active
WinActivate("Provisioning Services Device Optimization Tool")

;pause 3 seconds
Sleep(3000)
;click OK
ControlClick("Provisioning Services Device Optimization Tool", "", "[CLASS:Button; TEXT:&OK; INSTANCE:1]")

; Finished!</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->