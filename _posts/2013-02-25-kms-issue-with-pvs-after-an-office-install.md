---
id: 660
title: KMS issue with PVS after an Office install
date: 2013-02-25T15:19:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/02/25/kms-issue-with-pvs-after-an-office-install/
permalink: /2013/02/25/kms-issue-with-pvs-after-an-office-install/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/02/kms-issue-with-pvs-after-office-install.html
blogger_internal:
  - /feeds/7977773/posts/default/7631848997724237293
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Provisioning Services
  - scripting
---
We use Citrix Provisioning Services (PVS) 6.1 and it has been working well for us in our Citrix & environment. We could make versions, promote and patch without issue with our KMS servers. Then, we installed Office 2010 with KMS and now each time we make a version of our vDisk, then promote to test or production KMS becomes broken. Windows doesn't allow RDP sessions until it's resolved so this seriously borks our setup.

A fix [I read on this thread](http://forums.citrix.com/message.jspa?messageID=1674701) was to create a startup script that reinstalled the product key (as this key seems to go missing when promoting versions) and reactivate.

The script is as follows:

<pre class="lang:batch decode:true ">cscript //B "%windir%\
system32\slmgr.vbs" /ipk YC6KT-GKW9T-YTKYR-T4X34-R7VHC
cscript //B "%windir%\system32\slmgr.vbs" /ato</pre>

<div>
</div>

<div>
  For a simple test to see if this works I've added it to the machine startup script on the local system here:
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