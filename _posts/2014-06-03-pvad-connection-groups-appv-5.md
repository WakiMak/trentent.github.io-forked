---
id: 607
title: PVAD, Connection Groups, AppV 5
date: 2014-06-03T13:54:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/06/03/pvad-connection-groups-appv-5/
permalink: /2014/06/03/pvad-connection-groups-appv-5/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/06/pvad-connection-groups-appv-5.html
blogger_internal:
  - /feeds/7977773/posts/default/3943750590301235099
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
With AppV 5 and connection groups, a lesson I learned today is if you want to utilize connection groups you cannot use a valid PVAD path as the PVAD will not merge with VFS or another package's PVAD. Â So for any application you plan on using Add-on's or Middleware you MUST set the PVAD to something that is not used and install to the VFS for both the base package and the Add-on/middleware. Â If you use a PVAD what ends up happening is one of the packages in the connection group will "win" and be the only thing you see when you browse the PVAD path.

In the instance of this error I found, we sequenced VMWare and I wanted to add the VMware Update Manager as a add-on/plugin to the package. Â I used the same PVAD for both packages initially, "C:\Program Files (x86)\VMware" and when I published each seperately to a server I saw the proper file structure for each, when I made a connection group and published both to a single server and browsed to that PVAD I only saw the contents of one of the package, not the combination of both. Â I then resequenced the Add-on with a VFS path and it usurped the PVAD as it was not accessible nor the file systems merge.

I found one little blurb about this on WCA-8139.pptx on slide 38:Â PVAD is not merged in Connection Groups.  
http://video.ch9.ms/sessions/teched/na/2013/WCA-B319.pptx

There is also a [small blurb here detailing](http://blogs.msdn.com/b/gladiator/archive/2014/02/21/on-the-design-of-and-sequencing-for-connection-groups.aspx) that you should not install to PVAD if you plan on using Connection Groups.

"File Convergence

Only VFS Folders and tokenized paths merge. Pure and simple. If you suspect you will use an application or a plug-in within a connection group, you will need to sequence your application a specific way when it comes to selecting a PVAD (Primary Virtual Application Directory) and an installation directory. Unlike the former DSC (Dynamic Suite Composition) where the opposite was true, non-VFS folders do NOT merge. You will need to fully VFS the packages, that is, provide incorrect â€˜Primary Virtual Application Pathâ€™ (PVAD) while sequencing the packages. This puts all the files of the package under VFS folder and nothing ends up in the root folder. For example, use C: as the PVAD and install to its regular tokenized path (i.e. C:\Program Files, etc.) If the application does not normally install to a tokenized path, make sure you install to a different folder then the PVAD."

It's also important to note that registry keys will merge even if the underlying filesystem does not. Â This can make for some interesting customizations in applications that can be changed by registry only. Â You could publish the application to a generic group of users and then have two connection groups with two different packages and the difference between the packages is some registry keys that does something like change the server depending on the groups user location. Â This way a single package can serve multiple roles.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->