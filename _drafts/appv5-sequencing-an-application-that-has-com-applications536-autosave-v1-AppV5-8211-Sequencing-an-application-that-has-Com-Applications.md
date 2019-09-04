---
id: 974
title: 'AppV5 &#8211; Sequencing an application that has Com+ Applications'
date: 2016-04-24T16:42:24-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/536-autosave-v1/
permalink: /2016/04/24/536-autosave-v1/
---
AppV has a few weaknesses, one of them is Com+ Objects. Â It doesn&#8217;t like Com+ Objects and sequencing an application with them generally results in this error message:

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-fr_ilR5b3l8/VmHOXwyuu2I/AAAAAAAABPk/IE3U5AVgWWA/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.31.32%2BAM.png"><img src="http://1.bp.blogspot.com/-fr_ilR5b3l8/VmHOXwyuu2I/AAAAAAAABPk/IE3U5AVgWWA/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.31.32%2BAM.png" width="320" height="194" border="0" /></a>
</div>

<div>
</div>

<div>
  The Resolution seem easy enough:
</div>

<div>
  <div>
    <span style="font-family: 'courier new' , 'courier' , monospace;">1) Try extracting and installing the COM+ component natively on both sequencing station and the client</span>
  </div>
  
  <div>
    <span style="font-family: 'courier new' , 'courier' , monospace;">2) Thoroughly test your virtual application package to verify functionality.</span>
  </div>
</div>

<div>
</div>

<div>
  But how do you extract the COM+ components?
</div>

<div>
</div>

<div>
  1) Open &#8216;Component Services&#8217;
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-GNAXvOPoNV4/VmHO93uVuEI/AAAAAAAABPw/6BYHnfC4ugs/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.35.30%2BAM.png"><img src="http://4.bp.blogspot.com/-GNAXvOPoNV4/VmHO93uVuEI/AAAAAAAABPw/6BYHnfC4ugs/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.35.30%2BAM.png" width="320" height="268" border="0" /></a>
</div>

<div>
</div>

<div>
  2) Expand until you find your Com+ Applications, right-click and select &#8216;Export&#8217;
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-THvgS-mfFAk/VmHPXU6IGLI/AAAAAAAABP8/rNOqM483kdU/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.36.30%2BAM.png"><img src="http://1.bp.blogspot.com/-THvgS-mfFAk/VmHPXU6IGLI/AAAAAAAABP8/rNOqM483kdU/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.36.30%2BAM.png" width="253" height="320" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  3) Click &#8216;Next&#8217;
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-IsRz2gCUStY/VmHPuVMxj3I/AAAAAAAABQI/imEUAzfAGFc/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.38.32%2BAM.png"><img src="http://2.bp.blogspot.com/-IsRz2gCUStY/VmHPuVMxj3I/AAAAAAAABQI/imEUAzfAGFc/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.38.32%2BAM.png" width="320" height="245" border="0" /></a>
</div>

<div>
</div>

<div>
  4) Enter a path to save your MSI then click &#8216;Next&#8217;
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-P4fEZEZRGrc/VmHP8ZxMGrI/AAAAAAAABQU/jBH-Sn-CorU/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.39.26%2BAM.png"><img src="http://1.bp.blogspot.com/-P4fEZEZRGrc/VmHP8ZxMGrI/AAAAAAAABQU/jBH-Sn-CorU/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.39.26%2BAM.png" width="320" height="246" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  5) Click &#8216;Finish&#8217;
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-43lgG-YVn-M/VmHQJKViAAI/AAAAAAAABQg/YzZsobwHdGk/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.40.19%2BAM.png"><img src="http://2.bp.blogspot.com/-43lgG-YVn-M/VmHQJKViAAI/AAAAAAAABQg/YzZsobwHdGk/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.40.19%2BAM.png" width="320" height="243" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Since this application had two Com+ Applications I exported my second one as well:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-13vRhtUvEUY/VmHQvHAGw4I/AAAAAAAABQs/l9lgc58Adpc/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.42.24%2BAM.png"><img src="http://2.bp.blogspot.com/-13vRhtUvEUY/VmHQvHAGw4I/AAAAAAAABQs/l9lgc58Adpc/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.42.24%2BAM.png" width="320" height="195" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-v0SOZb9uR7Q/VmHQyafCdKI/AAAAAAAABQ4/Qe1dgs6mD9A/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.43.04%2BAM.png"><img src="http://3.bp.blogspot.com/-v0SOZb9uR7Q/VmHQyafCdKI/AAAAAAAABQ4/Qe1dgs6mD9A/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.43.04%2BAM.png" width="320" height="245" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  To ensure they work, I took them to our XenApp Test server and installed them.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-0cy2wBIvckk/VmHRl7oNMMI/AAAAAAAABRE/J88Ds2Ob2cU/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.46.21%2BAM.png"><img src="http://4.bp.blogspot.com/-0cy2wBIvckk/VmHRl7oNMMI/AAAAAAAABRE/J88Ds2Ob2cU/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.46.21%2BAM.png" width="320" height="124" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Both *appeared* to have installed correctly. Â But this application requires the Com+ Application to run under a service account. Â Checking the original install:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-mKI2FET3SfY/VmHSA13QWtI/AAAAAAAABRQ/EDKQBTCL1aQ/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.47.54%2BAM.png"><img src="http://3.bp.blogspot.com/-mKI2FET3SfY/VmHSA13QWtI/AAAAAAAABRQ/EDKQBTCL1aQ/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.47.54%2BAM.png" width="320" height="224" border="0" /></a>
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-HZuV-aAv8Js/VmHSCV5oTYI/AAAAAAAABRc/m-0t5_Tvzsw/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.48.11%2BAM.png"><img src="http://1.bp.blogspot.com/-HZuV-aAv8Js/VmHSCV5oTYI/AAAAAAAABRc/m-0t5_Tvzsw/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.48.11%2BAM.png" width="235" height="320" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: left;">
  I can see under the &#8216;Identity&#8217; tab that it is configured for &#8216;This user:&#8217;. Â I then checked the MSI install on the XenApp Test server and looked at the Identity tab:
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-5vTJ6kX-DBo/VmHScFTyh8I/AAAAAAAABRo/JzEGWezAWOA/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B10.48.22%2BAM.png"><img src="http://2.bp.blogspot.com/-5vTJ6kX-DBo/VmHScFTyh8I/AAAAAAAABRo/JzEGWezAWOA/s320/Screen%2BShot%2B2015-12-04%2Bat%2B10.48.22%2BAM.png" width="237" height="320" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  It appears it defaults to &#8216;System Account&#8217; even though I selected &#8216;Export user identities with roles&#8217;. Â To change the user account silently, I used this script supplied by the vendor (as a encrypted vbe):
</div>

<pre class="lang:vb decode:true ">'Setup variables
Const ApplicationName = "SFSQL"
Const AccountName = "HEALTHY\SVC_SharpFocus"
 
'Setup COM+ Application Catalog Object
Set Catalog = CreateObject("COMAdmin.COMAdminCatalog")
Set apps = Catalog.GetCollection("Applications")
'Populate the object with Applications from the COM Admin Catalog object
apps.populate
 
' Loop for each application in the array
For each app in apps
'Check for my application
    If app.Name = ApplicationName then
    ' If the application is found perform actions then create the roles object
        ' Clear the application Authentication Access Checks value
        app.Value("ApplicationAccessChecksEnabled") = false
        ' Set the application identity to be the Network Service
        app.Value("Identity") = "HEALTHY\SVC_SharpFocus"
                ' Set the application password for the network service account
                app.Value("Password") = "PASSWORDLOL"
        ' Save the changes to the application object
        apps.SaveChanges
    ' Escape the If statment
    End If
' Progress the application loop
Next</pre>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  This script changes the user account on the Com+ applications to use the proper account. Â When browsing the properties of the components of the SFSQL object I saw it referenced the dll to it:
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-SKJ2K7HBwNE/VmHVp2pi0NI/AAAAAAAABR0/ocKzICQNMzo/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B11.03.57%2BAM.png"><img src="http://1.bp.blogspot.com/-SKJ2K7HBwNE/VmHVp2pi0NI/AAAAAAAABR0/ocKzICQNMzo/s320/Screen%2BShot%2B2015-12-04%2Bat%2B11.03.57%2BAM.png" width="320" height="190" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: left;">
</div>

<div style="text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-EVZ6aLjlMrc/VmHVsHwXbKI/AAAAAAAABR8/8WT_hETGrUk/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B11.04.12%2BAM.png"><img src="http://3.bp.blogspot.com/-EVZ6aLjlMrc/VmHVsHwXbKI/AAAAAAAABR8/8WT_hETGrUk/s320/Screen%2BShot%2B2015-12-04%2Bat%2B11.04.12%2BAM.png" width="320" height="181" border="0" /></a>
</div>

&nbsp;

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Fortunately, it seems these files are captured by the MSI export of the Com+ Application.
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  To test everything now, I must now create my AppV package. Â Before doing the install I installed my prerequisites (the Com+ Applications) into the Sequencer so they are not captured by the package.
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  I ran this script to do my prereq install:
</div>

<div style="clear: both; text-align: left;">
</div>

<pre class="lang:batch decode:true ">:: ===========================================================================================================
::
:: Created by:  Trentent Tye
::   Intel Server Team
::   IBM Canada Ltd.
::
:: Creation Date: Dec 4, 2015
::
:: File Name:  AHS-SHARPFOCUS-PREFLIGHT.CMD
::
:: Description:  Configures Sharp Focus Prerequisites
::
:: ===========================================================================================================
::created by Trentent Tye
::default
if [%1] EQU [] (
  pushd %~dp0
 
  msiexec /i "SFSQL\SFSQL.MSI" /qb
  msiexec /i "SFSQLdotNET\SFSQLdotNET.msi" /qb
 
  cscript //nologo SFSQL_Account.vbe
  cscript //nologo SFSQLdotNET_Account.vbe
 
  popd
)
 
::add-package
if /I  [%1] EQU [A] (
  pushd %~dp0
 
  msiexec /i "SFSQL\SFSQL.MSI" /qb
  msiexec /i "SFSQLdotNET\SFSQLdotNET.msi" /qb
 
  cscript //nologo SFSQL_Account.vbe
  cscript //nologo SFSQLdotNET_Account.vbe
 
  popd
)
 
::remove-package
if /I [%1] EQU [R] (
  MsiExec.exe /X{61790351-7E04-4A76-88EC-AE93BE6D944D} /qb
  MsiExec.exe /X{66F3529A-9D0A-4187-A479-4791448CFE48} /qb
)</pre>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  Now I startup the sequencer and <a href="http://theorypc.ca/2015/07/03/appv5-sequencing-first-steps/">do my sequencing first steps</a>.
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  I selected a batch file I created for the install (per the vendor&#8217;s initial instructions):
</div>

<pre class="lang:batch decode:true ">:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::  
::  SharpFocus Install Sequence by Trentent Tye
::  Dec 4, 2015
::  
::  AppName: HealthlineSystems_SharpFocus_6.1.28.0_x86
::  
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
 
:: Prerequisites: Install the ComObjects first:
:: msiexec /i "D:\SharpFocus_Citrix_Install\ComObjects\SFSQL\SFSQL.MSI" /qb
:: msiexec /i "D:\SharpFocus_Citrix_Install\ComObjects\SFSQLdotNET\SFSQLdotNET.msi" /qb
 
pushd %~dp0
 
:: PreRequisites, J# 2.0
vjredist2.0.exe /q:a /c:"install /q"
 
:: This version of Sharp Focus is so old that I don't think they programmed in a silent install
:: be sure to install to "C:\Program Files (x86)\SFSQL"
SFSetup.exe
copy /y sf.EXE "C:\Program Files (x86)\SFSQL"
 
:: ComPlus Application - SFTSQL - not needed, captured with the prereq SFSQL.MSI
:: msiexec /i SFSQL_MTS.msi /q
 
::copy dlls to install folder
xcopy "d:\Sharpfocus_Citrix_Install\*.dll" "C:\Program Files (x86)\SFSQL"
xcopy "d:\Sharpfocus_Citrix_Install\richtx32.ocx" "C:\Program Files (x86)\SFSQL"
 
:: Register ComPlus Application - SFTSQLdotNET
cd /d "C:\Program Files (x86)\SFSQL"
%windir%\Microsoft.NET\Framework\v2.0.50727\RegSvcs /u MTSReporter.dll
%windir%\Microsoft.NET\Framework\v2.0.50727\RegSvcs MTSReporter.dll
%windir%\Microsoft.NET\Framework\v2.0.50727\RegSvcs /u SF_Reporter.dll
%windir%\Microsoft.NET\Framework\v2.0.50727\RegSvcs SF_Reporter.dll
 
:: Configure the ComPlus Applications to use a specific user account
cd /d %~dp0
cscript //nologo SFSQL_account.vbe
cscript //nologo SFSQLdotNET_account.vbe
 
:: Configure application network connections
regedit /s SFINSTALL_ServerSetup.reg
 
:: configure ODBC connections
regedit /s SFINSTALL_ODBC.reg
 
::register richtx32.ocx
regsvr32 /s "C:\Program Files (x86)\SFSQL\richtx32.ocx"
 
:: Create MyApps folders and icons and then cleanup
mkdir "C:\Users\Public\Desktop\MyApps\Sharp Focus"
mkdir "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Sharp Focus"
copy /y "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Healthline Systems\Sharp Focus SQL.lnk" "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\MyApps\Sharp Focus\Sharp Focus SQL.lnk"
copy /y "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Healthline Systems\Sharp Focus SQL.lnk" "C:\Users\Public\Desktop\MyApps\Sharp Focus\Sharp Focus SQL.lnk"
rmdir /s /q "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Healthline Systems"
rmdir /s /q "%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Healthline Systems"
 
pause
exit</pre>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-MB3MIrpsZ0k/VmICNOA8rwI/AAAAAAAABSQ/r1kQCmK_NRI/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.14.06%2BPM.png"><br /> </a><a style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-MB3MIrpsZ0k/VmICNOA8rwI/AAAAAAAABSQ/r1kQCmK_NRI/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.14.06%2BPM.png"><br /> </a>
</div>

<div style="text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em; text-align: left;" href="http://1.bp.blogspot.com/-MB3MIrpsZ0k/VmICNOA8rwI/AAAAAAAABSQ/r1kQCmK_NRI/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.14.06%2BPM.png"><img src="http://1.bp.blogspot.com/-MB3MIrpsZ0k/VmICNOA8rwI/AAAAAAAABSQ/r1kQCmK_NRI/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.14.06%2BPM.png" width="320" height="184" border="0" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: left;">
  Once the install completed I saved my package and took it to my XenApp Test server and published the application and ran it.
</div>

<div style="clear: both; text-align: left;">
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-DpEYRaR-p8M/VmIDvl92apI/AAAAAAAABSc/mNk3_41yKfo/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B1.45.19%2BPM.png"><img src="http://1.bp.blogspot.com/-DpEYRaR-p8M/VmIDvl92apI/AAAAAAAABSc/mNk3_41yKfo/s320/Screen%2BShot%2B2015-12-04%2Bat%2B1.45.19%2BPM.png" width="320" height="123" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      No, this is not correct
    </td>
  </tr>
</table>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
  For some reason my application was failing. Â I double checked the COM+ Applications are installed and appear to be working, so I opened Procmon.exe to see if I can find where it&#8217;s going wrong:
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="clear: both; text-align: left;">
</div>

<div style="text-align: center;">
  <a style="clear: left; display: inline !important; margin-bottom: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-xYfumEokKjc/VmIEIC_YYyI/AAAAAAAABSo/Kl7qR8yrMy4/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.01.49%2BPM.png"><img src="http://1.bp.blogspot.com/-xYfumEokKjc/VmIEIC_YYyI/AAAAAAAABSo/Kl7qR8yrMy4/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.01.49%2BPM.png" width="320" height="192" border="0" /></a>
</div>

<div style="text-align: center;">
</div>

<div style="text-align: left;">
  I&#8217;ve used procmon enough now to practically pull the old&#8217; Matrix, &#8220;<a href="https://www.youtube.com/watch?v=3vAnuBtyEYE">blonde, brunette, red head</a>&#8220;. Â Working from bottom to top, the message box is generated from the &#8216;Text&#8217; (LanguagePack) back to the KernelBase.dll. Â So we can exclude that has the cause, this is telling us *of* the issue. Â So if we look at the lines preceding it, one of them must be causing our message.
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-IxvM3boU_Ys/VmIGFH1Yl6I/AAAAAAAABS0/RvuFb9CJ_yY/s1600/error_in_here.png"><img src="http://1.bp.blogspot.com/-IxvM3boU_Ys/VmIGFH1Yl6I/AAAAAAAABS0/RvuFb9CJ_yY/s320/error_in_here.png" width="320" height="182" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  Looking at the first keys, the OLE keys, I can compare them to my sequencer and they match exactly, so the likelihood of them causing our issues is none.
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-ZZS7_eVkZBs/VmIGoTdIlDI/AAAAAAAABTA/b2KZDp55LnQ/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.33.06%2BPM.png"><img src="http://2.bp.blogspot.com/-ZZS7_eVkZBs/VmIGoTdIlDI/AAAAAAAABTA/b2KZDp55LnQ/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.33.06%2BPM.png" width="320" height="173" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  So we go back a little further to the AppID key and jumping to the *SUCCESS* location to look at it&#8217;s value&#8230;
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-6Dpu0l0m-Xw/VmIG4jDd_7I/AAAAAAAABTM/p19WdLhUrvE/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.34.12%2BPM.png"><img src="http://4.bp.blogspot.com/-6Dpu0l0m-Xw/VmIG4jDd_7I/AAAAAAAABTM/p19WdLhUrvE/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.34.12%2BPM.png" width="320" height="23" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-ncMFxDboq5Q/VmIHELaDgRI/AAAAAAAABTY/qHggVDpfLOk/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.34.59%2BPM.png"><img src="http://4.bp.blogspot.com/-ncMFxDboq5Q/VmIHELaDgRI/AAAAAAAABTY/qHggVDpfLOk/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.34.59%2BPM.png" width="320" height="80" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  I am on a server called WSCTXAPP301T but the registry key for this value is pointing to the system I did my sequencing on. Â I changed the value to the actual server name and did a registry search for &#8220;WSAPVSEQ07&#8221; to see if it appeared anywhere else. Â It did in one other location so I modified the value there as well to point to the local server:
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-B7ciQ5r67wI/VmIH5wGR_KI/AAAAAAAABTw/IzZxowTaWz4/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.36.51%2BPM.png"><img src="http://1.bp.blogspot.com/-B7ciQ5r67wI/VmIH5wGR_KI/AAAAAAAABTw/IzZxowTaWz4/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.36.51%2BPM.png" width="320" height="68" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-RGJUGheR_yI/VmIH2qHrrvI/AAAAAAAABTk/ETYzZittYEA/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.38.17%2BPM.png"><img src="http://2.bp.blogspot.com/-RGJUGheR_yI/VmIH2qHrrvI/AAAAAAAABTk/ETYzZittYEA/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.38.17%2BPM.png" width="320" height="68" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  And tested my application again:
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-q6nuDKEzBsY/VmIIJz-pkGI/AAAAAAAABT8/zW55hQ2um_c/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.39.39%2BPM.png"><img src="http://1.bp.blogspot.com/-q6nuDKEzBsY/VmIIJz-pkGI/AAAAAAAABT8/zW55hQ2um_c/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.39.39%2BPM.png" width="320" height="268" border="0" /></a>
  </div>
</div>

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  Success! Â The login screen. Â This is as far as I can take this app until I get credentials or a user to login and try testing for me. Â So, for now, I can create a preflight-script to setup some prerequisites when the application is published. Â I modified my preflight script to include the two registry values being updated:
</div>

&nbsp;

<div style="text-align: left;">
</div>

<div style="text-align: left;">
  Then I updated the Deployment_Config.xml file for this application:</p> 
  
  <p>
    Lastly, I logged onto a new, fresh, system and manually tested my XML file by publishing from the command line:
  </p>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-W4sYPfGjAmc/VmIM4Ibug5I/AAAAAAAABUM/1pkLEi3_Drg/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.47%2BPM.png"><img src="http://3.bp.blogspot.com/-W4sYPfGjAmc/VmIM4Ibug5I/AAAAAAAABUM/1pkLEi3_Drg/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.47%2BPM.png" width="320" height="100" border="0" /></a>
  </div>
  
  <p>
    Using Procmon to monitor to ensure the prerequisites get installed:
  </p>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-uvsw5dT_hGU/VmINj1qn_bI/AAAAAAAABUY/7vv5IIgtPx8/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.56.01%2BPM.png"><img src="http://3.bp.blogspot.com/-uvsw5dT_hGU/VmINj1qn_bI/AAAAAAAABUY/7vv5IIgtPx8/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.56.01%2BPM.png" width="320" height="262" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-bZI85v8wIy4/VmINnNUJflI/AAAAAAAABUk/2I1VEXbI-Ck/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.25%2BPM.png"><img src="http://2.bp.blogspot.com/-bZI85v8wIy4/VmINnNUJflI/AAAAAAAABUk/2I1VEXbI-Ck/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.25%2BPM.png" width="320" height="183" border="0" /></a>
  </div>
  
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td style="text-align: center;">
        <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-fibCCzBRB4Y/VmINqdn7CSI/AAAAAAAABUw/oW-mSlc4Te4/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.32%2BPM.png"><img src="http://1.bp.blogspot.com/-fibCCzBRB4Y/VmINqdn7CSI/AAAAAAAABUw/oW-mSlc4Te4/s320/Screen%2BShot%2B2015-12-04%2Bat%2B2.59.32%2BPM.png" width="320" height="132" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        &#8220;Exit Status: 0&#8221; &#8211;> Usually a good thing ðŸ™‚
      </td>
    </tr>
  </table>
  
  <p>
    Testing the application:
  </p>
  
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td style="text-align: center;">
        <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-6WnQorGYhpk/VmIOCDsFjqI/AAAAAAAABU8/Id8WO4oS408/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B3.04.41%2BPM.png"><img src="http://2.bp.blogspot.com/-6WnQorGYhpk/VmIOCDsFjqI/AAAAAAAABU8/Id8WO4oS408/s320/Screen%2BShot%2B2015-12-04%2Bat%2B3.04.41%2BPM.png" width="320" height="176" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        Looks good
      </td>
    </tr>
  </table>
  
  <p>
    Then testing removal and monitoring with Procmon:
  </p>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-Unb9u5n958Y/VmIOMlGrscI/AAAAAAAABVI/PYzlbjVbQ44/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B3.01.36%2BPM.png"><img src="http://2.bp.blogspot.com/-Unb9u5n958Y/VmIOMlGrscI/AAAAAAAABVI/PYzlbjVbQ44/s320/Screen%2BShot%2B2015-12-04%2Bat%2B3.01.36%2BPM.png" width="320" height="141" border="0" /></a>
  </div>
  
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td style="text-align: center;">
        <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-dQE6nOXvY_4/VmIOPAR9FdI/AAAAAAAABVU/mUrshY0EDzw/s1600/Screen%2BShot%2B2015-12-04%2Bat%2B3.02.26%2BPM.png"><img src="http://2.bp.blogspot.com/-dQE6nOXvY_4/VmIOPAR9FdI/AAAAAAAABVU/mUrshY0EDzw/s320/Screen%2BShot%2B2015-12-04%2Bat%2B3.02.26%2BPM.png" width="320" height="42" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        Com+ Application are removed cleanly ðŸ™‚
      </td>
    </tr>
  </table>
  
  <p>
    So everything looks good and I can now add this application to our AppV Management Server and deploy it to the machines that need it.
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->