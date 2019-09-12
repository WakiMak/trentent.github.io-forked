---
id: 681
title: AppV and Application Compatibility
date: 2012-03-13T16:21:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/03/13/appv-and-application-compatibility/
permalink: /2012/03/13/appv-and-application-compatibility/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/03/appv-and-application-compatibility.html
blogger_internal:
  - /feeds/7977773/posts/default/6183907285689439744
categories:
  - Blog
  - Uncategorized
tags:
  - "2003"
  - 2008R2
  - AppV
  - Citrix

  - XPSP3
---

```plaintext
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers]
"Q:\\Enterprise Reporting\\ErApp\\erapp32.exe"="WINXPSP3 256COLOR DISABLETHEMES DISABLEDWM HIGHDPIAWARE RUNASADMIN
```


I was having an issue with a old application that we want to run on our Citrix XenApp 6 farm; Microsoft Enterprise Reporting 7.5 SP4 (7.5.303). Namely, it wouldn't run. It's not compatible with Server 2008 R2 unless you're running SP5. Well, we're going to get rid of it in a few months but we want to get rid of our 4.5 farm. So, we need to migrate the application to XenApp 6 and Server 2008R2 from Presentation Server 4.5 and Server 2003 SP1.

First thing I did was setup a Server 2003 SP1 box and installed the AppV sequencer on it and sequenced the application. I then set it to run on 2008R2 64bit and moved the package over to it. It would crash. Analysing the crash logs would present to me the error... ERAPP32 was crashing its heap. In order to get it to work I had to set it to run in compatibility mode for XPSP3. Once I set this it worked flawlessly. So what I needed to do was push this fix to the rest of our Citrix servers before deploying the AppV application. If you've ever read ACT (application compatibilty toolkit) and merging it with AppV it's kind of a difficult job.

But there is a easier way.

Stored in the registry is the AppCompatFlags key that contains the applications and the shims you can apply to an application. If you put the path to your AppV application it will actually enable it to run in the compatibility mode that you specify. This was my registry entry:


```plaintext
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers]
"Q:\\Enterprise Reporting\\ErApp\\erapp32.exe"="WINXPSP3 256COLOR DISABLETHEMES DISABLEDWM HIGHDPIAWARE RUNASADMIN
```


And now the application works almost wonderfully (ER is a painful application)



<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->