---
id: 1138
title: Click the PVS Target Device Optimizer button with AutoIt
date: 2016-04-25T08:43:03-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/658-revision-v1/
permalink: /2016/04/25/658-revision-v1/
---
Short but sweet script.Â Disappointing that Citrix doesn&#8217;t have a silent run switch for this application.

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