---
id: 1079
title: 'Microsoft and Citrix putting pictures of scripts in documents&#8230;'
date: 2016-04-24T23:05:59-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/621-autosave-v1/
permalink: /2016/04/24/621-autosave-v1/
---
Come on guys, a little more effort than that.Â How are you supposed to copy paste an image?

The script is the following:

It&#8217;s supposed to pull the application and command-line to execute it in AppV5.Â I got the script from this document:  
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