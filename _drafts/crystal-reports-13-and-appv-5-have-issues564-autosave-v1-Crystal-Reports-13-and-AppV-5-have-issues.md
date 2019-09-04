---
id: 1002
title: Crystal Reports 13 and AppV 5 have issues
date: 2016-04-24T18:37:05-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/564-autosave-v1/
permalink: /2016/04/24/564-autosave-v1/
---
<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">Crystal Reports 13 and AppV 5 have issues. Â Crystal Reports 13 (CR) installs to some long paths which fail when loaded AppV5 tries to &#8216;integrate&#8217; them. Â A <a href="http://ryanwill.com/app-v-5-sp2-and-crystal-reports-runtime-13/"><span style="color: white; text-decoration: none; text-underline: none;">blog detailed shortening the installdir</span></a> path to something much shorter but CR still failed for me. Â My path without modifying the INSTALLDIR property was:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">C:\ProgramData\Microsoft\AppV\Client\Integration\14FA6B99-E7E2-4F5A-B7FC-4B024DE1705A\Root\VFS\ProgramFilesX86\SAP BusinessObects\Crystal Reports for .NET Framework 4.0\Common\SAP BusinessObjects Enterprise XI 4.0\win64_x64</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">224 Characters.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">With the modified INSTALLDIR it was:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">C:\ProgramData\Microsoft\AppV\Client\Integration\14FA6B99-E7E2-4F5A-B7FC-4B024DE1705A\Root\VFS\ProgramFilesX86\1\Crystal Reports for .NET Framework 4.0\Common\SAP BusinessObjects Enterprise XI 4.0\win64_x64</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">206 Characters.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">This still failed.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">The symptoms were errors in the application:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-dAKO2VmhbYw/VY0dIPApMBI/AAAAAAAAAyQ/aL8XiDgwd3Q/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.25%2BAM.png"><img src="http://4.bp.blogspot.com/-dAKO2VmhbYw/VY0dIPApMBI/AAAAAAAAAyQ/aL8XiDgwd3Q/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.25%2BAM.png" width="320" height="115" border="0" /></a>
</div>

&nbsp;

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Calibri; font-size: 11.0pt; mso-bidi-font-family: Calibri;">&#8216;The type initializer for &#8216;CrystalDecisions.CrystalReports.Engine.CRPE&#8217; threw an exception.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">Attempting to register the DLL&#8217;s related to these resulted in these errors:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-CRJt_P7MVag/VY0ddXW2TAI/AAAAAAAAAzU/Id5cskuAZUg/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.37.43%2BAM.png"><img src="http://4.bp.blogspot.com/-CRJt_P7MVag/VY0ddXW2TAI/AAAAAAAAAzU/Id5cskuAZUg/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.37.43%2BAM.png" width="320" height="127" border="0" /></a>
</div>

&nbsp;

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">[Window Title]</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">RegSvr32</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">[Content]</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">The module &#8220;clientdoc.dll&#8221; may not compatible with the version of Windows that you&#8217;re running. Check if the module is compatible with an x86 (32-bit) or x64 (64-bit) version of regsvr32.exe.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">[OK]</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Times; font-size: 13.0pt; mso-bidi-font-family: Times;">Since AppV5 integrates HKCR keys natively into the system, if you can&#8217;t register a DLL via a long path, then you will error with CR.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-JEq08HMRHFc/VY0dIBNpTZI/AAAAAAAAAyk/l3WchfyRUdI/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.35%2BAM.png"><img src="http://3.bp.blogspot.com/-JEq08HMRHFc/VY0dIBNpTZI/AAAAAAAAAyk/l3WchfyRUdI/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.35%2BAM.png" width="320" height="87" border="0" /></a>
</div>

&nbsp;

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">You can actually replicate a similar error by trying to &#8216;install&#8217; CR with a long path, so it&#8217;s not AppV5 that&#8217;s at fault, but apparently something within the CR DLL&#8217;s. Â Trying to install to a path similar in length to the AppV5 path will fail:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-7rTg-Xuesqg/VY0dIoNqaKI/AAAAAAAAAyo/lM-25wQrFyA/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.51%2BAM.png"><img src="http://4.bp.blogspot.com/-7rTg-Xuesqg/VY0dIoNqaKI/AAAAAAAAAyo/lM-25wQrFyA/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.51%2BAM.png" width="320" height="163" border="0" /></a>
</div>

&nbsp;

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">So what is the maximum length CR can be installed to?</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-nG5lvLyLsjE/VY0dIDu6TCI/AAAAAAAAAy8/c0eMV9-o9xY/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.46%2BAM.png"><img src="http://1.bp.blogspot.com/-nG5lvLyLsjE/VY0dIDu6TCI/AAAAAAAAAy8/c0eMV9-o9xY/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.46%2BAM.png" width="320" height="74" border="0" /></a>
</div>

&nbsp;

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">259 total characters. Â At 260 characters or greater the installer fails with an error similar to the above. I&#8217;m not sure why the AppV5 path is significantly less, but it is for some reason.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">In the end my path was:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">D:AppVDataPackageInstallationRoot14FA6B99-E7E2-4F5A-B7FC-4B024DE1705Aï¿½5D9D6C9-B77A-4376-94C6-C13E4764A8B0RootVFSProgramFilesX861CCommonXwin64_x64</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">158 Characters.</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">To do this I needed to modify the SAP Crystal Reports 13 file with a MST transform file. Â I did it by doing this:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
</div>

<table style="border-collapse: collapse; border: none; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-right-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; mso-padding-alt: 0cm 5.4pt 0cm 5.4pt; mso-table-layout-alt: fixed;" border="1" cellspacing="0" cellpadding="0">
  <tr style="mso-yfti-firstrow: yes; mso-yfti-irow: 0;">
    <td style="background: #16ADEE; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="bottom" width="57">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-align: justify; text-autospace: none; text-justify: inter-ideograph;">
        <b><span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Step Â </span></b>
      </div>
    </td>
    
    <td style="background: #16ADEE; border-left: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="bottom" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-align: justify; text-autospace: none; text-justify: inter-ideograph;">
        <b><span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Action</span></b>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 1;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l0 level1 lfo1; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">1. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Copy the ScreenTest III installation source files to a temporary folder C:swinst on the sequencing machine (WSAPVSEQ07 &#8211; Windows 2008 R2 SP1 in this case).</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 2;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l1 level1 lfo2; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">2. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Install <a href="https://msdn.microsoft.com/en-us/library/aa370557(v=vs.85).aspx"><span style="color: windowtext; text-decoration: none; text-underline: none;">Orca.msi</span></a></span>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 3;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l2 level1 lfo3; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">3. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Launch Orca and â€˜Openâ€™Â the Crystal Reports version</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 4;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l3 level1 lfo4; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">4. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Choose Transform from the menu > â€˜New Transformâ€™</span>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 5;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l4 level1 lfo5; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">5. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Select â€˜Directoryâ€™ and find the first instance of â€˜CrystalR|Crystal Reports for .NET Framework 4.0â€™</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-LVajiphsFOs/VY0eLlZLtHI/AAAAAAAAAzc/54hODnNqmac/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.40.43%2BAM.png"><img src="http://3.bp.blogspot.com/-LVajiphsFOs/VY0eLlZLtHI/AAAAAAAAAzc/54hODnNqmac/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.40.43%2BAM.png" width="320" height="136" border="0" /></a>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 6;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l5 level1 lfo6; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">6. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Double-click â€˜CrystalR|Crystal Reports for .NET Framework 4.0â€™ and copy the field.Â Select the menu â€˜Editâ€™ > â€˜Replaceâ€™ and set â€˜Find whatâ€™ as â€˜CrystalR|Crystal Reports for .NET Framework 4.0â€™ and â€˜Replace with:â€™ as â€˜CrystalR|Câ€™</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-o7e2d46XHxc/VY0dI6zXyMI/AAAAAAAAAyw/0cxBqRhW_gI/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.59%2BAM.png"><img src="http://1.bp.blogspot.com/-o7e2d46XHxc/VY0dI6zXyMI/AAAAAAAAAyw/0cxBqRhW_gI/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.32.59%2BAM.png" width="320" height="159" border="0" /></a>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">And click on â€˜Replace Allâ€™.Â If you get prompted:</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-unKFck9u5XA/VY0dJK2Uh8I/AAAAAAAAAy0/REfH_jUCSbo/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.03%2BAM.png"><img src="http://3.bp.blogspot.com/-unKFck9u5XA/VY0dJK2Uh8I/AAAAAAAAAy0/REfH_jUCSbo/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.03%2BAM.png" width="320" height="110" border="0" /></a>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Select â€˜Yesâ€™.</span>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 7;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l6 level1 lfo7; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">7. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Repeat the process for â€˜SAPBusin|SAP BusinessObjects Enterprise XI 4.0â€™Â to â€˜SAPBusin|Xâ€™</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 8;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l7 level1 lfo8; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">8. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Select â€˜Registryâ€™ and sort by â€˜Valueâ€™.Â Find the first instance of [INSTALLDIR]</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-TBH-DAoibEc/VY0dKjQtg_I/AAAAAAAAAzQ/Py8q6TkWd8I/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.12%2BAM.png"><img src="http://4.bp.blogspot.com/-TBH-DAoibEc/VY0dKjQtg_I/AAAAAAAAAzQ/Py8q6TkWd8I/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.12%2BAM.png" width="320" height="147" border="0" /></a>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 9;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l8 level1 lfo9; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">9. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Do a â€˜Replaceâ€™ and enter â€˜[INSTALLDIR]Crystal Reports for .NET Framework 4.0CommonSAP BusinessObjects Enterprise XI 4.0â€™ for â€˜Find whatâ€™ and â€˜Replace withâ€™ as â€˜[INSTALLDIR]CCommonXâ€™</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <div style="clear: both; text-align: center;">
          <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-9Z47qm2o3og/VY0dJDNUhCI/AAAAAAAAAzI/xcjEBqaJOJs/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.06%2BAM.png"><img src="http://4.bp.blogspot.com/-9Z47qm2o3og/VY0dJDNUhCI/AAAAAAAAAzI/xcjEBqaJOJs/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.06%2BAM.png" width="320" height="161" border="0" /></a>
        </div>
        
        <p>
          <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">And click on â€˜Replace Allâ€™</span>
        </p>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 10;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l9 level1 lfo10; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">10. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Result:</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-TBH-DAoibEc/VY0dKjQtg_I/AAAAAAAAAzQ/Py8q6TkWd8I/s1600/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.12%2BAM.png"><img src="http://4.bp.blogspot.com/-TBH-DAoibEc/VY0dKjQtg_I/AAAAAAAAAzQ/Py8q6TkWd8I/s320/Screen%2BShot%2B2015-06-26%2Bat%2B3.33.12%2BAM.png" width="320" height="147" border="0" /></a>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 11;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l10 level1 lfo11; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">11. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Select the menu â€˜Transformâ€™ > â€˜Generate Transformâ€™ and save the file as something like:</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">SAP_DIR_Short2.mst</span>
      </div>
    </td>
  </tr>
  
  <tr style="mso-yfti-irow: 12; mso-yfti-lastrow: yes;">
    <td style="border-top: none; border: solid #BFBFBF 1.0pt; mso-border-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 57.0pt;" valign="top" width="57">
      <div style="margin-left: 36.0pt; mso-layout-grid-align: none; mso-list: l11 level1 lfo12; mso-pagination: none; tab-stops: 11.0pt 36.0pt; text-autospace: none; text-indent: -36.0pt;">
        <!-- [if !supportLists]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt; mso-fareast-font-family: Arial;"><span style="mso-list: Ignore;">1<span style="font: 7.0pt 'Times New Roman';">Â Â </span></span></span><!--[endif]-->
        
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">12. </span>
      </div>
    </td>
    
    <td style="border-bottom: solid #BFBFBF 1.0pt; border-left: none; border-right: solid #BFBFBF 1.0pt; border-top: none; mso-border-alt: solid #BFBFBF .5pt; mso-border-left-alt: solid #BFBFBF .5pt; mso-border-top-alt: solid #BFBFBF .5pt; padding: 0cm 5.4pt 0cm 5.4pt; width: 434.0pt;" valign="top" width="434">
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
        <span lang="EN-US" style="font-family: Arial; font-size: 10.0pt;">Select the menu â€˜Transformâ€™ > â€˜Close Transformâ€™ and exit out of Orca.</span>
      </div>
      
      <div style="mso-layout-grid-align: none; mso-pagination: none; text-autospace: none;">
      </div>
    </td>
  </tr>
</table>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-align: justify; text-autospace: none; text-justify: inter-ideograph;">
  <span lang="EN-US" style="font-family: Arial; font-size: 13.0pt;">You can now install SAP CR with a command lineÂ similar to this:</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-align: justify; text-autospace: none; text-justify: inter-ideograph;">
  <span lang="EN-US" style="font-family: 'Courier New'; font-size: 10.0pt; mso-bidi-font-family: 'Courier New';">msiexec.exe /i &#8220;CRRuntime_64bit_13_0_14.msi&#8221; TRANSFORMS=SAP_DIR_Short2.mst INSTALLDIR=&#8221;C:Program Files (x86)1&#8243; /qb</span>
</div>

<div style="mso-layout-grid-align: none; mso-pagination: none; text-align: justify; text-autospace: none; text-justify: inter-ideograph;">
</div>

<!-- [if gte mso 9]><xml> <o:DocumentProperties>  <o:Revision>0</o:Revision>  <o:TotalTime>0</o:TotalTime>  <o:Pages>1</o:Pages>  <o:Words>578</o:Words>  <o:Characters>3297</o:Characters>  <o:Company>Theory PC</o:Company>  <o:Lines>27</o:Lines>  <o:Paragraphs>7</o:Paragraphs>  <o:CharactersWithSpaces>3868</o:CharactersWithSpaces>  <o:Version>14.0</o:Version> </o:DocumentProperties> <o:OfficeDocumentSettings>  <o:AllowPNG/> </o:OfficeDocumentSettings></xml><![endif]-->

<!-- [if gte mso 9]><xml> <w:WordDocument>  <w:View>Normal</w:View>  <w:Zoom>0</w:Zoom>  <w:TrackMoves/>  <w:TrackFormatting/>  <w:PunctuationKerning/>  <w:ValidateAgainstSchemas/>  <w:SaveIfXMLInval>false</w:SaveIfXMLInvalid>  <w:IgnoreMixedContent>false</w:IgnoreMixedContent>  <w:AlwaysShowPlaceholderText>false</w:AlwaysShowPlaceholderText>  <w:DoNotPromoteQF/>  <w:LidThemeOther>EN-US</w:LidThemeOther>  <w:LidThemeAsian>JA</w:LidThemeAsian>  <w:LidThemeComplexScript>X-NONE</w:LidThemeComplexScript>  <w:Compatibility>   <w:BreakWrappedTables/>   <w:SnapToGridInCell/>   <w:WrapTextWithPunct/>   <w:UseAsianBreakRules/>   <w:DontGrowAutofit/>   <w:SplitPgBreakAndParaMark/>   <w:EnableOpenTypeKerning/>   <w:DontFlipMirrorIndents/>   <w:OverrideTableStyleHps/>   <w:UseFELayout/>  </w:Compatibility>  <m:mathPr>   <m:mathFont m:val="Cambria Math"/>   <m:brkBin m:val="before"/>   <m:brkBinSub m:val="--"/>   <m:smallFrac m:val="off"/>   <m:dispDef/>   <m:lMargin m:val="0"/>   <m:rMargin m:val="0"/>   <m:defJc m:val="centerGroup"/>   <m:wrapIndent m:val="1440"/>   <m:intLim m:val="subSup"/>   <m:naryLim m:val="undOvr"/>  </m:mathPr></w:WordDocument></xml><![endif]-->

<!-- [if gte mso 9]><xml> <w:LatentStyles DefLockedState="false" DefUnhideWhenUsed="true" DefSemiHidden="true" DefQFormat="false" DefPriority="99" LatentStyleCount="276">  <w:LsdException Locked="false" Priority="0" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Normal"/>  <w:LsdException Locked="false" Priority="9" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="heading 1"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 2"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 3"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 4"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 5"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 6"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 7"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 8"/>  <w:LsdException Locked="false" Priority="9" QFormat="true" Name="heading 9"/>  <w:LsdException Locked="false" Priority="39" Name="toc 1"/>  <w:LsdException Locked="false" Priority="39" Name="toc 2"/>  <w:LsdException Locked="false" Priority="39" Name="toc 3"/>  <w:LsdException Locked="false" Priority="39" Name="toc 4"/>  <w:LsdException Locked="false" Priority="39" Name="toc 5"/>  <w:LsdException Locked="false" Priority="39" Name="toc 6"/>  <w:LsdException Locked="false" Priority="39" Name="toc 7"/>  <w:LsdException Locked="false" Priority="39" Name="toc 8"/>  <w:LsdException Locked="false" Priority="39" Name="toc 9"/>  <w:LsdException Locked="false" Priority="35" QFormat="true" Name="caption"/>  <w:LsdException Locked="false" Priority="10" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Title"/>  <w:LsdException Locked="false" Priority="1" Name="Default Paragraph Font"/>  <w:LsdException Locked="false" Priority="11" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Subtitle"/>  <w:LsdException Locked="false" Priority="22" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Strong"/>  <w:LsdException Locked="false" Priority="20" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Emphasis"/>  <w:LsdException Locked="false" Priority="59" SemiHidden="false" UnhideWhenUsed="false" Name="Table Grid"/>  <w:LsdException Locked="false" UnhideWhenUsed="false" Name="Placeholder Text"/>  <w:LsdException Locked="false" Priority="1" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="No Spacing"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 1"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 1"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 1"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 1"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 1"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 1"/>  <w:LsdException Locked="false" UnhideWhenUsed="false" Name="Revision"/>  <w:LsdException Locked="false" Priority="34" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="List Paragraph"/>  <w:LsdException Locked="false" Priority="29" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Quote"/>  <w:LsdException Locked="false" Priority="30" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Intense Quote"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 1"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 1"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 1"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 1"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 1"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 1"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 1"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 1"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 2"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 2"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 2"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 2"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 2"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 2"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 2"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 2"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 2"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 2"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 2"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 2"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 2"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 2"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 3"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 3"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 3"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 3"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 3"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 3"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 3"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 3"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 3"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 3"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 3"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 3"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 3"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 3"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 4"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 4"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 4"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 4"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 4"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 4"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 4"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 4"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 4"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 4"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 4"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 4"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 4"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 4"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 5"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 5"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 5"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 5"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 5"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 5"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 5"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 5"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 5"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 5"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 5"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 5"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 5"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 5"/>  <w:LsdException Locked="false" Priority="60" SemiHidden="false" UnhideWhenUsed="false" Name="Light Shading Accent 6"/>  <w:LsdException Locked="false" Priority="61" SemiHidden="false" UnhideWhenUsed="false" Name="Light List Accent 6"/>  <w:LsdException Locked="false" Priority="62" SemiHidden="false" UnhideWhenUsed="false" Name="Light Grid Accent 6"/>  <w:LsdException Locked="false" Priority="63" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 1 Accent 6"/>  <w:LsdException Locked="false" Priority="64" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Shading 2 Accent 6"/>  <w:LsdException Locked="false" Priority="65" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 1 Accent 6"/>  <w:LsdException Locked="false" Priority="66" SemiHidden="false" UnhideWhenUsed="false" Name="Medium List 2 Accent 6"/>  <w:LsdException Locked="false" Priority="67" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 1 Accent 6"/>  <w:LsdException Locked="false" Priority="68" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 2 Accent 6"/>  <w:LsdException Locked="false" Priority="69" SemiHidden="false" UnhideWhenUsed="false" Name="Medium Grid 3 Accent 6"/>  <w:LsdException Locked="false" Priority="70" SemiHidden="false" UnhideWhenUsed="false" Name="Dark List Accent 6"/>  <w:LsdException Locked="false" Priority="71" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Shading Accent 6"/>  <w:LsdException Locked="false" Priority="72" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful List Accent 6"/>  <w:LsdException Locked="false" Priority="73" SemiHidden="false" UnhideWhenUsed="false" Name="Colorful Grid Accent 6"/>  <w:LsdException Locked="false" Priority="19" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Subtle Emphasis"/>  <w:LsdException Locked="false" Priority="21" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Intense Emphasis"/>  <w:LsdException Locked="false" Priority="31" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Subtle Reference"/>  <w:LsdException Locked="false" Priority="32" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Intense Reference"/>  <w:LsdException Locked="false" Priority="33" SemiHidden="false" UnhideWhenUsed="false" QFormat="true" Name="Book Title"/>  <w:LsdException Locked="false" Priority="37" Name="Bibliography"/>  <w:LsdException Locked="false" Priority="39" QFormat="true" Name="TOC Heading"/> </w:LatentStyles></xml><![endif]-->

<!-- [if gte mso 10]><![endif]-->

<!--StartFragment-->

<!--EndFragment-->

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->