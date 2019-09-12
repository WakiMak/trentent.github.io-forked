---
id: 542
title: AppV5 - Integrating Certificates into your AppV package
date: 2015-10-20T13:20:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/10/20/appv5-integrating-certificates-into-your-appv-package/
permalink: /2015/10/20/appv5-integrating-certificates-into-your-appv-package/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/10/appv5-integrating-certificates-into.html
blogger_internal:
  - /feeds/7977773/posts/default/4504970249644526114
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
These are the steps I've found to sequence root certificates into your AppV5 application.

```batch
certmgr.exe -add AddTrustCert.cer -s -r localMachine root
```

Where do you get [certmgr.exe](https://msdn.microsoft.com/en-us/library/e78byta0%28v=vs.110%29.aspx?f=255&MSPPError=-2147217396) from?

The [visual studio downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) apparently contains this tool.

Once you've started your sequencer and run the command it will add the certificate to these two places:  
HKLM\SOFTWARE\Microsoft\SystemCertificates\ROOT\Certificates\  
HKLM\SOFTWARE\Wow6432Node\Microsoft\SystemCertificates\ROOT\Certificates

And that's how you add certificates to a sequenced package.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->