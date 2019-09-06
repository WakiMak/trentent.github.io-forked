---
id: 661
title: AppV 5 issues
date: 2013-02-21T13:33:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/02/21/appv-5-issues/
permalink: /2013/02/21/appv-5-issues/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/02/appv-5-issues.html
blogger_internal:
  - /feeds/7977773/posts/default/5846391315858359445
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
We are having issues with AppV 5.Â Suddenly, after enabling global refreshes the application we were publishing was having major problems.Â From everything I have seen the publishing server and management server were operating correctly, the client appeared to be operating correctly but I kept getting these types of errors on the client:

<pre class="lang:default decode:true ">"Package {571b8b47-d4ba-491e-a824-6b64260a8205} version {2ccba72b-ef64-4041-a2c3-d24768165b69} failed configuration in folder 'D:\AppVData\PackageInstallationRoot\571B8B47-D4BA-491E-A824-6B64260A8205\2CCBA72B-EF64-4041-A2C3-D24768165B69' with error 0x4FC01304-0x80070003."

"Error encountered while executing command Get-AppvPublishingServer | Sync-AppvPublishingServer. Error: There were errors encountered when trying to publish packages from the server. 
Operation attempted: RefreshPublishingServer.  
AppV Error Code: 070000000B. "</pre>

To resolve the issue I needed to execute:

<pre class="lang:ps decode:true ">Remove-AppvClientPackage * 
Get-AppvPublishingServer | Sync-AppvPublishingServer</pre>

On the client

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->