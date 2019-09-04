---
id: 594
title: Installing Oculus Rift SDK on Windows Server OS (or bypass the product type check with most apps)
date: 2014-09-02T23:50:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/09/02/installing-oculus-rift-sdk-on-windows-server-os-or-bypass-the-product-type-check-with-most-apps/
permalink: /2014/09/02/installing-oculus-rift-sdk-on-windows-server-os-or-bypass-the-product-type-check-with-most-apps/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/09/installing-oculus-rift-sdk-on-windows.html
blogger_internal:
  - /feeds/7977773/posts/default/1548109896672475820
categories:
  - Blog
  - Uncategorized
tags:
  - Uncategorized
---
<div style="clear: both; text-align: center;">
</div>

I run a server OS as my primary OS for my home lab, which is a hyper-v system. &nbsp;I'm not interested in dual-booting this system just to run a consumer OS so I can play with my Oculus Rift. &nbsp;So I explored bypassing these checks and this is how I was able to get my software to think it was running on a consumer OS.

First thing you need is [Application Verifier](http://www.microsoft.com/en-ca/download/details.aspx?id=20028). &nbsp;This utility allows you to customize shims for applications. &nbsp;Once it's installed, go "File > Add Application" and add your application.

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-m7b5eoThPmU/VAap-z0u-kI/AAAAAAAAAhg/JRy25vFuSGs/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.40.46%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-m7b5eoThPmU/VAap-z0u-kI/AAAAAAAAAhg/JRy25vFuSGs/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.40.46%2BPM.png" height="259" width="320" /></a>
</div>

Under "Test" expand out compatibility, check "HighVersionLie" then right-click on it and select "Properties"

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-hN5vj8IJa9A/VAaqckyal4I/AAAAAAAAAho/OXQXGKgse68/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.42.11%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://4.bp.blogspot.com/-hN5vj8IJa9A/VAaqckyal4I/AAAAAAAAAho/OXQXGKgse68/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.42.11%2BPM.png" height="257" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

[It's here that you can now determine what "Product Type"](http://blogs.msdn.com/b/wit/archive/2008/11/04/white-lies-using-appverifier-to-test-os-versioning.aspx) this application will recognize the underlying OS as. &nbsp;The values for Product Type are:

<table border="1" cellpadding="0" cellspacing="0" style="background-color: #ced5db; border-collapse: collapse; border: medium none; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 12px; line-height: 18px; margin: auto auto auto 5.35pt;">
  <tr>
    <td style="background-color: transparent; border: 1pt solid rgb(163, 163, 163); padding: 1.95pt 3pt; width: 155.3pt;" valign="top" width="207">
      <div style="margin: 0cm 0cm 0pt;">
        <b><span style="color: #000066; font-family: Verdana, sans-serif; font-size: 8.5pt;">Value<o:p></o:p></span></b>
      </div>
    </td>
    
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(163, 163, 163) rgb(163, 163, 163) rgb(163, 163, 163) rgb(240, 240, 240); border-right-style: solid; border-right-width: 1pt; border-top-style: solid; border-top-width: 1pt; padding: 1.95pt 3pt; width: 371.15pt;" valign="top" width="495">
      <div style="margin: 0cm 0cm 0pt;">
        <b><span style="color: #000066; font-family: Verdana, sans-serif; font-size: 8.5pt;">Meaning<o:p></o:p></span></b>
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163); border-left-style: solid; border-left-width: 1pt; border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 155.3pt;" valign="top" width="207">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">VER_NT_DOMAIN_CONTROLLER<o:p></o:p></span>
      </div>
      
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">0x0000002<o:p></o:p></span>
      </div>
    </td>
    
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163) rgb(240, 240, 240); border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 371.15pt;" valign="top" width="495">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">The system is a domain controller and the operating system is Windows Server&nbsp;2008, Windows Server&nbsp;2003, or Windows&nbsp;2000 Server.<o:p></o:p></span>
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163); border-left-style: solid; border-left-width: 1pt; border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 155.3pt;" valign="top" width="207">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">VER_NT_SERVER<o:p></o:p></span>
      </div>
      
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">0x0000003<o:p></o:p></span>
      </div>
    </td>
    
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163) rgb(240, 240, 240); border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 371.15pt;" valign="top" width="495">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">The operating system is Windows Server&nbsp;2008, Windows Server&nbsp;2003, or Windows&nbsp;2000 Server.<o:p></o:p></span>
      </div>
      
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">Note that a server that is also a domain controller is reported as VER_NT_DOMAIN_CONTROLLER, not VER_NT_SERVER.<o:p></o:p></span>
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163); border-left-style: solid; border-left-width: 1pt; border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 155.3pt;" valign="top" width="207">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">VER_NT_WORKSTATION<o:p></o:p></span>
      </div>
      
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">0x0000001<o:p></o:p></span>
      </div>
    </td>
    
    <td style="background-color: transparent; border-bottom-style: solid; border-bottom-width: 1pt; border-color: rgb(240, 240, 240) rgb(163, 163, 163) rgb(163, 163, 163) rgb(240, 240, 240); border-right-style: solid; border-right-width: 1pt; padding: 1.95pt 3pt; width: 371.15pt;" valign="top" width="495">
      <div style="margin: 0cm 0cm 0pt;">
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;">The operating system is Windows&nbsp;Vista, Windows&nbsp;XP Professional, Windows&nbsp;XP Home Edition, or Windows&nbsp;2000 Professional.<o:p></o:p></span>
      </div>
      
      <div>
        <span style="color: black; font-family: Verdana, sans-serif; font-size: 8.5pt;"><br /></span>
      </div>
    </td>
  </tr>
</table>

Specifying a value of 1 in Product Type simulates that this is a consumer/workstation OS and the Oculus Rift software now installs without issue.

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-v4_o8zz0v00/VAasBeh7DyI/AAAAAAAAAh0/xqnZdOhOv7U/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.49.16%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://1.bp.blogspot.com/-v4_o8zz0v00/VAasBeh7DyI/AAAAAAAAAh0/xqnZdOhOv7U/s1600/Screen%2BShot%2B2014-09-02%2Bat%2B11.49.16%2BPM.png" height="119" width="320" /></a>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->