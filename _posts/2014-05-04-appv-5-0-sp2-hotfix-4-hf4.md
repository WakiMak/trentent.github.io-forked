---
id: 611
title: AppV 5.0 SP2 HotFix 4 (HF4)
date: 2014-05-04T11:47:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/05/04/appv-5-0-sp2-hotfix-4-hf4/
permalink: /2014/05/04/appv-5-0-sp2-hotfix-4-hf4/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/05/appv-50-sp2-hotfix-4-hf4.html
blogger_internal:
  - /feeds/7977773/posts/default/4792907409704757017
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
I upgraded our test environment for AppV 5.0 SP2 to HF4 Â and came across an issue where our packages won&#8217;t load. Â The error specifically was:

<pre class="lang:default decode:true ">"Package {248b49bd-39f9-471c-b655-7483c11a6f69} version {868b72ed-ac56-4b8e-ae42-60a049401330} failed configuration in folder 'D:\AppVData\PackageInstallationRoot\' with error 0x4C40330C-0x12."</pre>

<div>
</div>

<div>
  We got this error for all of our packages. Â I procmon&#8217;ed it and it appeared to be OK, saying that it was creating folders and loading the .appv package, but when I went into the D:AppVDataPackageInstallationRoot folder, nothing was in there.
</div>

<div>
</div>

<div>
  It turns out that with Hotfix 4 Microsoft has changed the PackageInstallationRoot permissions to require Authenticated Users (Read/Write) to the folder, without this ACL we get the above error. Â Microsoft appears to have made this change to allow Globally published application to be able to be launched on network shares without requiring the local machine account added to the share. Â Unfortunately, without the ACL on the local folder we get the error above. Â To create the folder with the correct ACL&#8217;s, just delete the existing folder and restart the AppVClient service. Â The service will auto-create the folder with the correct ACL&#8217;s.
</div>

<div>
  If the service does not recreate the folder, you may need to run the AppV publishing scheduled task to get it created.
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->