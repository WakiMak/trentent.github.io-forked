---
id: 538
title: AppV5 - Using Application Compatibility Toolkit to solve issues
date: 2015-11-26T18:16:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/11/26/appv5-using-application-compatibility-toolkit-to-solve-issues/
permalink: /2015/11/26/appv5-using-application-compatibility-toolkit-to-solve-issues/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/11/appv5-using-application-compatibility.html
blogger_internal:
  - /feeds/7977773/posts/default/7773662978389980683
categories:
  - Blog
  - Uncategorized
---
C:

We have several applications that install folders in the root of the C: drive.  For most AppV5 implementations, this wouldn't be an issue but we modify our [PackageInstallationRoot](http://trentent.blogspot.ca/2014/08/appv-5-changing-packageinstallationroot.html) folder so the token {AppVPackageDrive} turns into the drive letter specified in the PackageInstallationRoot registry key.  For us, that's the D: drive.  This causes an issue because when you sequence an application to C: the application has an expectation for it to be there.  Ideally, AppV takes care of that by the use of the tokens, but this breaks down when applications are hardcoded.

<div>
</div>

<div>
  So far, we've been able to work around these issues by using junctions or setting the PVAD to the folder as the PVAD acts literally on the value specified, essentially becoming a hard coded, custom, token.
</div>

<div>
</div>

<div>
  But now we have an application with THREE folders that are installed on the root of the C: and the application is hard-coded to look for two of them.
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://3.bp.blogspot.com/-ds9TNSg_9u8/VleUaSXKBjI/AAAAAAAABME/fQ25v_Gs6I8/s1600/1.tiff"><img src="http://3.bp.blogspot.com/-ds9TNSg_9u8/VleUaSXKBjI/AAAAAAAABME/fQ25v_Gs6I8/s400/1.tiff" width="400" height="260" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Hello my nemesis's
    </td>
  </tr>
</table>

<div>
</div>

<div>
</div>

<div>
  If we do nothing this is the error we get after attempting to login to the program:
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-CLFS2DT77zw/VleVXfTsaqI/AAAAAAAABMM/-yVub2SXB7s/s1600/2.tiff"><img src="http://3.bp.blogspot.com/-CLFS2DT77zw/VleVXfTsaqI/AAAAAAAABMM/-yVub2SXB7s/s320/2.tiff" width="320" height="109" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Looking at the appvve we can see the D: is coming up with those folders.
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-xDE7D8NdfGw/VleWsCHDZuI/AAAAAAAABMY/aerZXFoFAYE/s1600/3.tiff"><img src="http://2.bp.blogspot.com/-xDE7D8NdfGw/VleWsCHDZuI/AAAAAAAABMY/aerZXFoFAYE/s320/3.tiff" width="320" height="215" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  So this isn't going to work.  How can we make it work?
</div>

<div>
</div>

<div>
  Enter <a href="http://www.microsoft.com/en-ca/download/details.aspx?id=7352">Microsoft Application Compatibility Toolkit</a>
</div>

<div>
</div>

<div>
  After publishing this application on the server, we install ACT on the Citrix server.
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-qhmTtmH1ivY/VleaxxaO6mI/AAAAAAAABMk/0C8rBzTFtr4/s1600/4.tiff"><img src="http://2.bp.blogspot.com/-qhmTtmH1ivY/VleaxxaO6mI/AAAAAAAABMk/0C8rBzTFtr4/s320/4.tiff" width="320" height="245" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Launch the "Compatibility Administrator (32-bit)"
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-RxrDCjCDt3o/VlebM8Z6OrI/AAAAAAAABMs/3LNwR85hdbQ/s1600/5.tiff"><img src="http://2.bp.blogspot.com/-RxrDCjCDt3o/VlebM8Z6OrI/AAAAAAAABMs/3LNwR85hdbQ/s320/5.tiff" width="278" height="320" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
</div>

<div>
  Right-click on "New Database" and select "Create New > Application Fix"
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-QrOCo1pIRKA/Vlebn3xUHBI/AAAAAAAABM0/VQAtx3cM68Q/s1600/6.tiff"><img src="http://2.bp.blogspot.com/-QrOCo1pIRKA/Vlebn3xUHBI/AAAAAAAABM0/VQAtx3cM68Q/s320/6.tiff" width="320" height="217" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Enter the details and browse to the application and click "Next"
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-WA30qZMLBis/Vleb84m4sZI/AAAAAAAABM8/2ykgSSA6h28/s1600/7.tiff"><img src="http://4.bp.blogspot.com/-WA30qZMLBis/Vleb84m4sZI/AAAAAAAABM8/2ykgSSA6h28/s320/7.tiff" width="320" height="281" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Click 'Next' on the Compatibility Mode screen
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-YcaTnzoE4AA/Vlecxtv8ZmI/AAAAAAAABNE/v5odYX2VpR8/s1600/8.tiff"><img src="http://2.bp.blogspot.com/-YcaTnzoE4AA/Vlecxtv8ZmI/AAAAAAAABNE/v5odYX2VpR8/s320/8.tiff" width="320" height="280" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Select 'CorrectFilePaths' and then click 'Parameters'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-8FhblV8OMKI/VledQjdJ0DI/AAAAAAAABNM/yVdKsK4AG5w/s1600/9.tiff"><img src="http://2.bp.blogspot.com/-8FhblV8OMKI/VledQjdJ0DI/AAAAAAAABNM/yVdKsK4AG5w/s320/9.tiff" width="320" height="280" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  In command line, enter the path that should exist, then a semi-colon divider ";" and then the target path:
</div>

<div>
  C:\webforms;C:\ProgramData\Microsoft\AppV\Client\Integration\A3E3C00D-19F1-47CB-9BA1-464DB85DC3CA\Root\VFS\AppVPackageDrive\webforms
</div>

<div>
  and click "OK"
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-MZpk5YnpvtE/VleeJ7cqlqI/AAAAAAAABNU/_cD-X-cXoTU/s1600/10.tiff"><img src="http://4.bp.blogspot.com/-MZpk5YnpvtE/VleeJ7cqlqI/AAAAAAAABNU/_cD-X-cXoTU/s320/10.tiff" width="304" height="320" border="0" /></a>
</div>

<div>
</div>

<div>
  Try a Test Run.
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-9KgYirFi0o4/VleenZfJN8I/AAAAAAAABNc/9AfgXhmjOEU/s1600/11.tiff"><img src="http://3.bp.blogspot.com/-9KgYirFi0o4/VleenZfJN8I/AAAAAAAABNc/9AfgXhmjOEU/s320/11.tiff" width="320" height="137" border="0" /></a>
</div>

<div>
</div>

<div>
  And the application launches without any error messages!
</div>

<div>
</div>

<div>
  Click 'Next'
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-kNDtzMdLBsw/Vlee5Nhqo_I/AAAAAAAABNk/tn3isPPCgFU/s1600/12.tiff"><img src="http://2.bp.blogspot.com/-kNDtzMdLBsw/Vlee5Nhqo_I/AAAAAAAABNk/tn3isPPCgFU/s320/12.tiff" width="320" height="281" border="0" /></a>
</div>

<div>
</div>

<div>
  Then Finish.
</div>

<div>
</div>

<div>
  Click 'Save' then name your database and click "OK":
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-KpaMQM0r6-M/VlefOZOndrI/AAAAAAAABNs/JUDk2c_eQsA/s1600/13.tiff"><img src="http://1.bp.blogspot.com/-KpaMQM0r6-M/VlefOZOndrI/AAAAAAAABNs/JUDk2c_eQsA/s320/13.tiff" width="320" height="301" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Save your fix now:
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-mTa1-fvseqg/Vlefe5bFKfI/AAAAAAAABN0/CYvXMW8p48M/s1600/14.tiff"><img src="http://2.bp.blogspot.com/-mTa1-fvseqg/Vlefe5bFKfI/AAAAAAAABN0/CYvXMW8p48M/s320/14.tiff" width="320" height="215" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  At this stage you now need to put your SDB file somewhere accessible for when the package is published.  We put it on a fileshare.
</div>

<div>
</div>

<div>
  Now, all we need to is install the fix.
</div>

<div>
</div>

<div>
  Since we publish our application globally, I added the fix to the DeploymentConfig.xml:
</div>

<div>
  <pre class="lang:default decode:true "><machinescripts>
    <addpackage>
      <path>sdbinst.exe</Path>
      <arguments>-q "\\healthy.bewell.ca\apps\appv\PortableApps\AppCompat_Fixes\PRIS_WEBFORMS_FIX.sdb"</Arguments>
      <wait RollbackOnError="true" Timeout="30"/>
    </AddPackage>
    <removepackage>
<path>sdbinst.exe</Path>
<arguments>-u "\\healthy.bewell.ca\apps\appv\PortableApps\AppCompat_Fixes\PRIS_WEBFORMS_FIX.sdb"</Arguments>
      <wait RollbackOnError="false" Timeout="60"/>
    </RemovePackage>
  </MachineScripts></pre>
</div>

<div>
  <div style="color: #008f00; font-family: Helvetica; font-size: 10px; line-height: normal;">
  </div>
  
  <div style="color: #0433ff; font-family: Helvetica; font-size: 10px; line-height: normal;">
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-kX6UD-SBrfk/VlegbyD5-3I/AAAAAAAABOA/9yZMai3mg08/s1600/15.tiff"><img src="http://1.bp.blogspot.com/-kX6UD-SBrfk/VlegbyD5-3I/AAAAAAAABOA/9yZMai3mg08/s320/15.tiff" width="320" height="75" border="0" /></a>
  </div>
  
  <p>
    And we are done! The application now works.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->