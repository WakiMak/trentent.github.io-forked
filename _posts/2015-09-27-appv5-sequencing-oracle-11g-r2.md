---
id: 544
title: AppV5 - Sequencing Oracle 11g R2
date: 2015-09-27T01:55:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/27/appv5-sequencing-oracle-11g-r2/
permalink: /2015/09/27/appv5-sequencing-oracle-11g-r2/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/appv5-sequencing-oracle-11g-r2.html
blogger_internal:
  - /feeds/7977773/posts/default/1295914877496596150
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
Apparently this is a fun topic.  How [do you sequence Oracle 11G R2 on AppV5](https://social.technet.microsoft.com/Forums/en-US/b39d8600-28f2-40db-b38d-160bb551cd45/sequencing-oracle-odbc-drivers-with-appv-5?forum=mdopappv)?

I believe I have an answer.  In my attempts to sequence Oracle 11G on AppV5 I came across a few issues and have come up with solutions that work for various applications that rely on this tool.

**The first issue:**  
Oracle 11G only allows paths without spaces and special characters.

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <span style="margin-left: auto; margin-right: auto;"><a href="http://docs.oracle.com/cd/E29407_01/install.111200/e23737/installing_software.htm"><img src="http://3.bp.blogspot.com/-IBRIoaDFm18/VgeBGfH0WDI/AAAAAAAABJc/3AdIUnp1K3A/s320/oracle_11g.PNG" width="320" height="144" border="0" /></a></span>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <span style="background-color: white; font-family: Tahoma, sans-serif; font-size: x-small; text-align: left;"><a href="http://docs.oracle.com/cd/E29407_01/install.111200/e23737/installing_software.htm">On Windows systems, if the path to your Java installation includes a space character, you must provide the path in DOS 8.3 format, as shown in the previous example.</a></span>
    </td>
  </tr>
</table>

This \*maybe\* fixed now, but I experienced issues with trying to install Oracle 11g to the 8.3 folder structure to place it under "Program Files" or "Program Files (x86)".  The sequenced application would be broken.  [This was a known/reported issue with AppV 5SP2 HF4](http://trentent.blogspot.ca/2014/08/appv-5-short-file-names-are-not-created.html) that was marked as 'fixed' by Microsoft for SP3+.  I have not had the ability to confirm that and will continue this post with what I know works...  I also believe that when expanded out it uses the full path with spaces as opposed to the 8.3 path.

**Second Issue:**  
Installing Oracle 11G to the default 'recommended' directory will fail if you move your [PackageInstallationRoot](http://trentent.blogspot.ca/2014/08/appv-5-changing-packageinstallationroot.html) to a different drive.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-FKIBo_haoeM/VgeC1X8-TSI/AAAAAAAABJo/Dt6B1pwncpA/s1600/oracle2.PNG"><img src="http://1.bp.blogspot.com/-FKIBo_haoeM/VgeC1X8-TSI/AAAAAAAABJo/Dt6B1pwncpA/s320/oracle2.PNG" width="320" height="142" border="0" /></a>
</div>

This is because the second folder (apps - in this example) is not tokenized.  Forcing AppV5 to utilize the token "appvPackgeDrive" which can expand out differently then you expect, breaking the application.

**Third Issue:**  
Cannot install to PVAD.

The reason I chose to NOT utilize PVAD is if you do then Oracle [cannot be used in connection groups](http://trentent.blogspot.ca/2014/06/pvad-connection-groups-appv-5.html).

**So how do you resolve all these issues and sequence Oracle 11G R2?**

The direction I went was to ensure the directory I sequenced the installer to was a tokenized directory.  It also needed to a directory that, when expanded in the virtualized environment, does not contain any spaces or special characters.

The list of directories AppV5 tokenize's can be found in the AppV 5.0 Sequencing Guide.

I'll list them here:

<table style="border-collapse: collapse; border: none; mso-border-alt: solid #7BA0CD 1.0pt; mso-border-themecolor: accent1; mso-border-themetint: 191; mso-padding-alt: 0in 5.4pt 0in 5.4pt; mso-yfti-tbllook: 1184;" border="1" cellspacing="0" cellpadding="0">
  <tr>
    <td style="background: #4F81BD; border-right: none; border: solid #7BA0CD 1.0pt; mso-background-themecolor: accent1; mso-border-themecolor: accent1; mso-border-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b><span style="color: white; mso-themecolor: background1;">Known Folder Token</span></b>
      </div>
    </td>
    
    <td style="background: #4F81BD; border-left: none; border: solid #7BA0CD 1.0pt; mso-background-themecolor: accent1; mso-border-themecolor: accent1; mso-border-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        <b><span style="color: white; mso-themecolor: background1;">Known Folder Path</span></b>
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AccountPictures</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsAccountPictures
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Administrative Tools</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsStart MenuProgramsAdministrative Tools
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppData</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoaming
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Application Shortcuts</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsApplication Shortcuts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Cache</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsTemporary Internet Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CD Burning</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsBurnBurn
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Administrative Tools</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsStart MenuProgramsAdministrative Tools
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common AppData</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramData
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Desktop</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicDesktop
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Documents</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicDocuments
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Programs</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsStart MenuPrograms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Start Menu</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsStart Menu
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Startup</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsStart MenuProgramsStartup
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Common Templates</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsTemplates
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CommonDownloads</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicDownloads
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CommonMusic</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicMusic
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CommonPictures</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicPictures
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CommonRingtones</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsRingtones
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CommonVideo</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicVideos
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Contacts</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersContacts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Cookies</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsCookies
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CredentialManager</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftCredentials
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>CryptoKeys</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftCrypto
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Desktop</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersDesktop
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Device Metadata Store</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsDeviceMetadataStore
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>DocumentsLibrary</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibrariesDocuments.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Downloads</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersDownloads
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>DpapiKeys</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftProtect
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Favorites</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersFavorites
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Fonts</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowsFonts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>GameTasks</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsGameExplorer
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>History</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsHistory
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ImplicitAppShortcuts</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftInternet ExplorerQuick LaunchUser PinnedImplicitAppShortcuts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Libraries</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibraries
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Links</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersLinks
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Local AppData</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocal
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>LocalAppDataLow</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalLow
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>MusicLibrary</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibrariesMusic.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>My Music</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersMusic
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>My Pictures</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPictures
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>My Video</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersVideos
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>NetHood</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsNetwork Shortcuts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Personal</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersDocuments
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>PicturesLibrary</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibrariesPictures.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Podcast Library</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibrariesPodcasts.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Podcasts</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPodcasts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>PrintHood</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsPrinter Shortcuts
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Profile</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Users
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFiles</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFilesCommon</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program FilesCommon Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFilesCommonX64</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program FilesCommon Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFilesCommonX86</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program Files (x86)Common Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFilesX64</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program Files
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ProgramFilesX86</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Program Files (x86)
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Programs</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsStart MenuPrograms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Public</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublic
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>PublicAccountPictures</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicAccountPictures
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>PublicGameTasks</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:ProgramDataMicrosoftWindowsGameExplorer
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>PublicLibraries</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicLibraries
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Quick Launch</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftInternet ExplorerQuick Launch
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Recent</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsRecent
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>RecordedTVLibrary</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersPublicLibrariesRecordedTV.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>ResourceDir</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowsresources
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Ringtones</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsRingtones
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Roamed Tile Images</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsRoamedTileImages
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Roaming Tiles</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataLocalMicrosoftWindowsRoamingTiles
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>SavedGames</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersSaved Games
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Searches</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersSearches
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>SendTo</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsSendTo
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Start Menu</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsStart Menu
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Startup</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsStart MenuProgramsStartup
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>System</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>SystemCertificates</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftSystemCertificates
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>SystemX86</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowsSysWOW64
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Templates</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsTemplates
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>User Pinned</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftInternet ExplorerQuick LaunchUser Pinned
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>UserProfiles</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:Users
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>VideosLibrary</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAppDataRoamingMicrosoftWindowsLibrariesVideos.library-ms
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Windows</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windows
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>Custom Token</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        Custom Token Expansion
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVAllUsersDir</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:UsersAll Users
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVComputerName</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        -LT02
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVCurrentUserSID</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        S-1-5-21-124525095-708259637-1543119021-705252
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVEnvironmentVariableCommonProgramFiles</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        %commonprogramfiles%
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVEnvironmentVariableProgramFiles</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        %ProgramFiles%
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVPackageDrive</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVPackageRoot</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:AppInstallFolder
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32Catroot</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32catroot
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32Catroot2</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32catroot2
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32DriversEtc</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32driversetc
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32Driverstore</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32driverstore
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32Logfiles</b>
      </div>
    </td>
    
    <td style="border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32logfiles
      </div>
    </td>
  </tr>
  
  <tr>
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: solid #7BA0CD 1.0pt; border-right: none; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-left-themecolor: accent1; mso-border-left-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 116.95pt;" valign="top" width="156">
      <div>
        <b>AppVSystem32Spool</b>
      </div>
    </td>
    
    <td style="background: #D3DFEE; border-bottom: solid #7BA0CD 1.0pt; border-left: none; border-right: solid #7BA0CD 1.0pt; border-top: none; mso-background-themecolor: accent1; mso-background-themetint: 63; mso-border-bottom-themecolor: accent1; mso-border-bottom-themetint: 191; mso-border-right-themecolor: accent1; mso-border-right-themetint: 191; mso-border-top-alt: solid #7BA0CD 1.0pt; mso-border-top-themecolor: accent1; mso-border-top-themetint: 191; padding: 0in 5.4pt 0in 5.4pt; width: 361.85pt;" valign="top" width="482">
      <div>
        C:windowssystem32spool
      </div>
    </td>
  </tr>
</table>

&nbsp;

<div>
  There are multiple directories we can choose.  I opted to use "Common AppData".  That means I will install the Oracle client here: "C:ProgramData".  It does not contain a space, is tokenized, and when expanded will remain on the C: drive.  I created a 'response' file for the Oracle install.
</div>

<div>
  
```config
###############################################################################
## Copyright(c) Oracle Corporation 1998,2008. All rights reserved.           ##
##                                                                           ##
## Specify values for the variables listed below to customize                ##
## your installation.                                                        ##
##                                                                           ##
## Each variable is associated with a comment. The comment                   ##
## can help to populate the variables with the appropriate                   ##
## values.                    ##
##                                                                           ##
###############################################################################
 
#-------------------------------------------------------------------------------
# Do not change the following system generated value. 
#-------------------------------------------------------------------------------
oracle.install.responseFileVersion=http://www.oracle.com/2007/install/rspfmt_clientinstall_response_schema_v11_2_0
 
#-------------------------------------------------------------------------------
# This variable holds the hostname of the system as set by the user. 
# It can be used to force the installation to use an alternative   
# hostname rather than using the first hostname found on the system
# (e.g., for systems with multiple hostnames and network interfaces).
ORACLE_HOSTNAME=wsapvseq07.healthy.bewell.ca
#-------------------------------------------------------------------------------
# Unix group to be set for the inventory directory.  
UNIX_GROUP_NAME=
#-------------------------------------------------------------------------------
# Inventory location.
INVENTORY_LOCATION=C:\Program Files (x86)\Oracle\Inventory
#-------------------------------------------------------------------------------
# Specify the languages in which the components will be installed.             
#
# en   : English                  ja   : Japanese                  
# fr   : French                   ko   : Korean                    
# ar   : Arabic                   es   : Latin American Spanish    
# bn   : Bengali                  lv   : Latvian                   
# pt_BR: Brazilian Portuguese     lt   : Lithuanian                
# bg   : Bulgarian                ms   : Malay                     
# fr_CA: Canadian French          es_MX: Mexican Spanish           
# ca   : Catalan                  no   : Norwegian                 
# hr   : Croatian                 pl   : Polish                    
# cs   : Czech                    pt   : Portuguese                
# da   : Danish                   ro   : Romanian                  
# nl   : Dutch                    ru   : Russian                   
# ar_EG: Egyptian                 zh_CN: Simplified Chinese        
# en_GB: English (Great Britain)  sk   : Slovak                    
# et   : Estonian                 sl   : Slovenian                 
# fi   : Finnish                  es_ES: Spanish                   
# de   : German                   sv   : Swedish                   
# el   : Greek                    th   : Thai                      
# iw   : Hebrew                   zh_TW: Traditional Chinese       
# hu   : Hungarian                tr   : Turkish                   
# is   : Icelandic                uk   : Ukrainian                 
# in   : Indonesian               vi   : Vietnamese                
# it   : Italian                                                   
#
# Example : SELECTED_LANGUAGES=en,fr,ja
SELECTED_LANGUAGES=en
#-------------------------------------------------------------------------------
# Complete path of the Oracle Home  
ORACLE_HOME=C:\ProgramData\Oracle\product\11.2.0\client_1
#-------------------------------------------------------------------------------
# Complete path of the Oracle Base. 
ORACLE_BASE=C:\ProgramData\Oracle
#------------------------------------------------------------------------------
#Name       : INSTALL_TYPE
#Datatype   : String
#Description: Installation type of the component.
#
#             The following choices are available. The value should contain
#             only one of these choices.
#             InstantClient : InstantClient
#             Administrator : Administrator
#             Runtime       : Runtime
#             Custom        : Custom
#
#Example    : INSTALL_TYPE = "Administrator"
#------------------------------------------------------------------------------
oracle.install.client.installType=Custom
#-------------------------------------------------------------------------------
# Name       : oracle.install.client.customComponents
# Datatype   : StringList
#
# This property is considered only if INSTALL_TYPE is set to "Custom"
#
# Description: List of Client Components you would like to install
#
#   The following choices are available. You may specify any
#   combination of these choices.  The components you choose should
#   be specified in the form "internal-component-name:version"
#   Below is a list of components you may specify to install.
#
# oracle.sqlj:11.2.0.1.0 -- "Oracle SQLJ"
# oracle.rdbms.util:11.2.0.1.0 -- "Oracle Database Utilities"
# oracle.javavm.client:11.2.0.1.0 -- "Oracle Java Client"
# oracle.sqlplus:11.2.0.1.0 -- "SQL*Plus"
# oracle.dbjava.jdbc:11.2.0.1.0 -- "Oracle JDBC/THIN Interfaces"
# oracle.ldap.client:11.2.0.1.0 -- "Oracle Internet Directory Client"
# oracle.rdbms.oci:11.2.0.1.0 -- "Oracle Call Interface (OCI)"
# oracle.precomp:11.2.0.1.0 -- "Oracle Programmer"
# oracle.xdk:11.2.0.1.0 -- "Oracle XML Development Kit"
# oracle.network.aso:11.2.0.1.0 -- "Oracle Advanced Security"
# oracle.assistants.oemlt:11.2.0.1.0 -- "Enterprise Manager Minimal Integration"
# oracle.oraolap.mgmt:11.2.0.1.0 -- "OLAP Analytic Workspace Manager and Worksheet"
# oracle.network.client:11.2.0.1.0 -- "Oracle Net"
# oracle.ordim.client:11.2.0.1.0 -- "Oracle Multimedia Client Option"
# oracle.ons:11.2.0.0.0 -- "Oracle Notification Service"
# oracle.odbc:11.2.0.1.0 -- "Oracle ODBC Driver"
# oracle.has.client:11.2.0.1.0 -- "Oracle Clusterware High Availability API"
# oracle.dbdev:11.2.0.1.0 -- "Oracle SQL Developer"
# oracle.rdbms.scheduler:11.2.0.1.0 -- "Oracle Scheduler Agent"
#
# Example    : oracle.install.client.customComponents="oracle.precomp:11.2.0.1.0","oracle.ons:11.2.0.0.0","oracle.oraolap.mgmt:11.2.0.1.0","oracle.rdbms.scheduler:11.2.0.1.0"
#-------------------------------------------------------------------------------
oracle.install.client.customComponents=oracle.network.client:11.2.0.1.0,oracle.odbc:11.2.0.1.0
#-------------------------------------------------------------------------------
#Name       : MTS_PORT
#Datatype   : int 
#Description: Port number to be used for by the Oracle MTS Recovery Service to listen
#    for requests. This needs to be entered in case oracle.ntoramts is 
#         selected in the list of custom components in custom install
#
#
#Example    : MTS_PORT = 2030
#------------------------------------------------------------------------------
oracle.install.client.oramtsPortNumber=49155
 
#------------------------------------------------------------------------------
# Host name to be used for by the Oracle Scheduler Agent.
# This needs to be entered in case oracle.rdbms.scheduler is selected in the
# list of custom components during custom install
#
# Example    : oracle.install.client.schedulerAgentHostName = acme.domain.com
#------------------------------------------------------------------------------
oracle.install.client.schedulerAgentHostName=
 
#------------------------------------------------------------------------------
# Port number to be used for by the Oracle Scheduler Agent.
# This needs to be entered in case oracle.rdbms.scheduler is selected in the
# list of custom components during custom install
#
# Example: oracle.install.client.schedulerAgentPortNumber = 1500
#------------------------------------------------------------------------------
oracle.install.client.schedulerAgentPortNumber
```

</div>

<div>
</div>

<div>
</div>

<div>
  I called the script through this command:
</div>

<div>
```shell
setup.exe -nowait -nowelcome -silent -responseFile "%~dp0\silent.rsp"
ping 127.0.0.1 -n 60 >NUL
copy /y *.ora "C:\ProgramData\Oracle\product\11.2.0\client_1\network\admin"
```
  
  <p>
    And that's it!
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->