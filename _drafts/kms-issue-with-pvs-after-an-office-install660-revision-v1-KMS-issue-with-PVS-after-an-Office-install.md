---
id: 1141
title: KMS issue with PVS after an Office install
date: 2016-04-25T08:46:08-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/660-revision-v1/
permalink: /2016/04/25/660-revision-v1/
---
We use Citrix Provisioning Services (PVS) 6.1 and it has been working well for us in our Citrix XenApp environment.Â We could make versions, promote and patch without issue with our KMS servers.Â Then, we installed Office 2010 with KMS and now each time we make a version of our vDisk, then promote to test or production KMS becomes broken.Â Windows doesn&#8217;t allow RDP sessions until it&#8217;s resolved so this seriously borks our setup.

A fix [I read on this thread](http://forums.citrix.com/message.jspa?messageID=1674701) was to create a startup script that reinstalled the product key (as this key seems to go missing when promoting versions) and reactivate.

The script is as follows:

<pre class="lang:batch decode:true ">cscript //B "%windir%\
system32\slmgr.vbs" /ipk YC6KT-GKW9T-YTKYR-T4X34-R7VHC
cscript //B "%windir%\system32\slmgr.vbs" /ato</pre>

<div>
</div>

<div>
  For a simple test to see if this works I&#8217;ve added it to the machine startup script on the local system here:
</div>

<div>
  C:\windows\system32\GroupPolicy\Machine\Scripts\Startup
</div>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->