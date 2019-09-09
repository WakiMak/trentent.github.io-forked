---
id: 621
title: Microsoft and Citrix putting pictures of scripts in documents...
date: 2013-11-20T12:50:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/11/20/microsoft-and-citrix-putting-pictures-of-scripts-in-documents/
permalink: /2013/11/20/microsoft-and-citrix-putting-pictures-of-scripts-in-documents/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/11/microsoft-and-citrix-putting-pictures.html
blogger_internal:
  - /feeds/7977773/posts/default/3367586350373732757
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerShell
  - scripting
---
Come on guys, a little more effort than that. How are you supposed to copy paste an image?

The script is the following:

It's supposed to pull the application and command-line to execute it in AppV5. I got the script from this document:  
<http://www.microsoft.com/en-us/download/details.aspx?id=40885>

<pre class="lang:ps decode:true ">"PackageName,Application Name,ApplicationPath"|out-file .\appPath.txt -Append
foreach($pkg in (gwmi -Namespace root\AppV -Class AppVClientPackage -Filter "IsPublishedGlobally = true"))
{

    $apps=gwmi -Namespace root\AppV -Class AppVClientApplication -Filter "PackageID = '$($pkg.PackageId)' and PackageVersionID = '$($pkg.VersionId)'"

   foreach($app in $apps)

   {

      $rootfolder=(Get-ItemProperty "HKLM:\Software\Microsoft\AppV\Client\Packages\$($app.PackageID)\Versions\$($app.PackageVersionID)\Catalog").Folder

      $appPath=$app.TargetPath.Replace("[{AppVPackageRoot}]",$rootFolder)

      "$($pkg.Name),$($app.Name),$($appPath)"|out-file .\AppPath.txt -Append
   }
}
</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->