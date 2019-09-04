---
id: 709
title: 'invalid pxe server list format &#8211; Altiris'
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
<div style="text-align: center;">
</div>

I restarted our Altiris server and our PXE services wouldn&#8217;t come up. Trying to start them resulted in:

<div>
  File not found
</div>

<div>
</div>

<div>
  I then checked the path listed in the service and found that, indeed, our PXE files where not in the location that the service was trying to start them in:
</div>

<div>
  F:\Program Files\Altiris\eXpress\Deployment Server\PXE
</div>

<div>
</div>

<div>
  The missing files were:
</div>

<div>
  PXEService.exe
</div>

<div>
  PXEmtftp.exe
</div>

<div>
  PXEMgr.exe
</div>

<div>
  PXECfgService.exe
</div>

<div>
</div>

<div>
  They were located here:
</div>

<div>
  F:\Program Files\Altiris\eXpress\Deployment Server\PXE\MasterImages\UpSrv\51
</div>

<div>
</div>

<div>
  I don&#8217;t know why it wasn&#8217;t looking for them in the longer path, but I copied those files to the directory it wanted (PXE)
</div>

<div>
</div>

<div>
  I then attempted to start the services and they all started correctly.
</div>

<div>
</div>

<div>
  Then I attempted to PXE boot one of my VM&#8217;s. This failed with an error stating:
</div>

<div>
  &#8220;invalid pxe server list format&#8221;
</div>

<div>
</div>

<div>
  Attempting to troubleshoot this, I used procmon and saw that it was downloading bstrap.0 successfully then generating the error. I enabled logging for the PXE Server in Altiris and set the logging level for &#8220;Errors&#8221;. I then restarted all the Altiris services. When I restarted the PXE Server service, I got this error message:
</div>

<div>
  <div>
    E [11:32:26 05/03] (3480): Enter: SetupDHCP(&#8230;)
  </div>
  
  <div>
    E [11:32:26 05/03] (3480): SetupDHCP: Auto Detect, configure option 60.
  </div>
  
  <div>
    (3480)Failed to load Dll Library.
  </div>
  
  <div>
    (3480)Failed to load Dll Library.
  </div>
  
  <div>
    (3480)Failed to load Dll Library.
  </div>
  
  <div>
    (3480)Failed to load Dll Library.
  </div>
</div>

<div>
</div>

<div>
  I then fired up Process Monitor and did a file trace while restarting PXE Server. It informed me it could not find the following files:
</div>

<div>
  <span style="color: #0000ee; -webkit-text-decorations-in-effect: underline;"><img id="BLOGGER_PHOTO_ID_5602518807153903218" style="display: block; text-align: center; cursor: pointer; width: 400px; height: 47px; margin: 0px auto 10px auto;" src="http://4.bp.blogspot.com/-bEZSmTuh4aE/TcAlK_PahnI/AAAAAAAAAGI/Dy3oG7fbIDE/s400/Screen%2Bshot%2B2011-05-03%2Bat%2B9.53.47%2BAM.png" alt="" border="0" /></span>
</div>

<div>
  <span style="color: #0000ee;"><span style="color: #000000;">Â </span></span>
</div>

<div>
  <span class="Apple-style-span">I then copied those missing DLL&#8217;s from the </span>F:\Program Files\Altiris\eXpress\Deployment Server\PXE\MasterImages\UpSrv\51 directory to the F:\Program Files\Altiris\eXpress\Deployment Server\PXE directory and restarted the PXE Server Service.
</div>

<div>
</div>

<div>
  I then attempted to PXE boot my VM and lo and behold, it worked again.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->