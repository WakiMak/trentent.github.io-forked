---
id: 709
title: invalid pxe server list format - Altiris
date: 2011-05-03T09:42:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/05/03/invalid-pxe-server-list-format-altiris/
permalink: /2011/05/03/invalid-pxe-server-list-format-altiris/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/05/invalid-pxe-server-list-format-altiris.html
blogger_internal:
  - /feeds/7977773/posts/default/1730417403915859941
categories:
  - Blog
  - Uncategorized
tags:
  - procmon
  - scripting
---
I restarted our Altiris server and our PXE services wouldn't come up. Trying to start them resulted in:
File not found

I then checked the path listed in the service and found that, indeed, our PXE files where not in the location that the service was trying to start them in:
F:\Program Files\Altiris\eXpress\Deployment Server\PXE

The missing files were:
PXEService.exe
PXEmtftp.exe
PXEMgr.exe
PXECfgService.exe

They were located here:
F:\Program Files\Altiris\eXpress\Deployment Server\PXE\MasterImages\UpSrv\51

I don't know why it wasn't looking for them in the longer path, but I copied those files to the directory it wanted (\PXE)

I then attempted to start the services and they all started correctly.

Then I attempted to PXE boot one of my VM's. This failed with an error stating:
"invalid pxe server list format"

Attempting to troubleshoot this, I used procmon and saw that it was downloading bstrap.0 successfully then generating the error. I enabled logging for the PXE Server in Altiris and set the logging level for "Errors". I then restarted all the Altiris services. When I restarted the PXE Server service, I got this error message:
E [11:32:26 05/03] (3480): Enter: SetupDHCP(...)
E [11:32:26 05/03] (3480): SetupDHCP: Auto Detect, configure option 60.
(3480)Failed to load Dll Library.
(3480)Failed to load Dll Library.
(3480)Failed to load Dll Library.
(3480)Failed to load Dll Library.

I then fired up Process Monitor and did a file trace while restarting PXE Server. It informed me it could not find the following files:

<img>http://4.bp.blogspot.com/-bEZSmTuh4aE/TcAlK_PahnI/AAAAAAAAAGI/Dy3oG7fbIDE/s400/Screen%2Bshot%2B2011-05-03%2Bat%2B9.53.47%2BAM.png</img>

F:\Program Files\Altiris\eXpress\Deployment Server\PXE\MasterImages\UpSrv\51 directory to the F:\Program Files\Altiris\eXpress\Deployment Server\PXE directory and restarted the PXE Server Service.

I then attempted to PXE boot my VM and lo and behold, it worked again.