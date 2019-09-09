---
id: 615
title: EdgeSight Load Testing with a Single User account (& 6.5)
date: 2014-04-18T12:05:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/04/18/edgesight-load-testing-with-a-single-user-account-&-6-5/
permalink: /2014/04/18/edgesight-load-testing-with-a-single-user-account-&-6-5/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/04/edgesight-load-testing-with-single-user.html
blogger_internal:
  - /feeds/7977773/posts/default/801626555061706320
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Receiver

---
I've been doing some EdgeSight Load Testing with & 6.5; which I haven't done in a few months.  We have updated our Citrix servers since then with the newer HotFix RollUp (HFRU) pack and now I'm testing a new application.  Our typical setup is to use a single generic account and connect it via multiple sessions to the same Citrix server and capture some metrics (CPU, Network, Memory, etc).  In the past, this seemed to work pretty flawlessly but in the last week I was having issues.  By the time the 3rd session was connecting to the Citrix server, instead of creating a new session it would 'steal' the 1st session.  This prevented me from having more than 2 sessions connected to a server.

Well, it turns out Citrix modified their behaviour a bit but allowed a registry key to be set to change it back.  The new value was introduced in HFRU 2 and is as follows:

http://support.citrix.com/article/CTX136248

On Windows Server 2003, you can specify whether a user can reconnect to a session from any client device or only from the originating client. The From originating client only option is not present anymore in the Remote Desktop Services settings of the Windows Server 2008 R2 edition.  
This feature enhancement allows you to implement the same functionality on Windows Server 2008 R2 through &. To enable the feature, you must set the following registry key:

<pre class="lang:reg decode:true ">HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Policies\Citrix
Name: ReconnectSame
Type: REG_DWORD
Data: 1
</pre>

Note that if Workspace Control is enabled on the Web Interface, enabling the "Automatically reconnect to sessions when users log on" feature can result in side effects and users might have to launch the session twice to open a session from the non-originating client device.

Enabling that registry key and I can immediately restart my load testing without any stolen sessions.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->