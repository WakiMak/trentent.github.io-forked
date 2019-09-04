---
id: 983
title: 'AppV5 &#8211; Integrating Certificates into your AppV package'
date: 2016-04-24T16:59:00-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/542-revision-v1/
permalink: /2016/04/24/542-revision-v1/
---
These are the steps I&#8217;ve found to sequence root certificates into your AppV5 application.

<pre class="lang:batch decode:true ">certmgr.exe -add AddTrustCert.cer -s -r localMachine root</pre>

Where do you get [certmgr.exe](https://msdn.microsoft.com/en-us/library/e78byta0%28v=vs.110%29.aspx?f=255&MSPPError=-2147217396) from?

The [visual studio downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) apparently contains this tool.

Once you&#8217;ve started your sequencer and run the command it will add the certificate to these two places:  
HKLM\SOFTWARE\Microsoft\SystemCertificates\ROOT\Certificates\  
HKLM\SOFTWARE\Wow6432Node\Microsoft\SystemCertificates\ROOT\Certificates

And that&#8217;s how you add certificates to a sequenced package.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->