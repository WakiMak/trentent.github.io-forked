---
id: 1783
title: 'Office Deployment Toolkit &#8211; taking a look'
date: 2016-10-21T12:59:53-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/10/21/1587-revision-v1/
permalink: /2016/10/21/1587-revision-v1/
---
When I attempted to create a virtualized Office application using the Office Deployment Toolkit (ODT) I was happily surprised at how easy it was, and extremely disappointed that it contained a lot of &#8216;baggage&#8217;. That is, the virtualized Office application installed options that I did not have a choice on whether they should be there or not. These included things like &#8216;Proofing Tools&#8217;, &#8216;Office 2013 Upload Center&#8217;, &#8216;Telemetry Dashboard for Office 2013&#8217; etc.

<img class="aligncenter size-full wp-image-1588" src="http://theorypc.ca/wp-content/uploads/2016/07/1.png" alt="1" width="264" height="192" /> 

I wanted to get rid of nearly all of this &#8216;junk&#8217;. Â Unfortunately, Microsoft only offers the [following apps to be excluded](https://technet.microsoft.com/library/jj219426(v=office.15).aspx#BKMK_ExcludeAppElement):

<pre class="lang:default decode:true ">Access
Excel
Groove
InfoPath
Lync
OneDrive
OneNote
Outlook
PowerPoint
Project
Publisher
SharePointDesigner
Visio
Word</pre>

So if I wanted to remove these extra apps, which should I select?

&nbsp;

This I have not been able to figure out. Â BUT what you can do is modify the mcxml&#8217;s after you&#8217;ve built a package once, removing all these extra programs, and run the &#8216;flattener.exe&#8217; again. Â This will remake your package without them. Â So the steps in detail:

  1. Create your config.xml file and exclude everything: <pre class="lang:xhtml decode:true">&lt;Configuration&gt;
  &lt;Add SourcePath="\\wsapvseq07\d$\Office_2013\2013ProPlus" OfficeClientEdition="32" &gt;
    &lt;Product ID="ProPlusVolume"&gt;
      &lt;Language ID="en-us" /&gt;
     &lt;ExcludeApp ID= "Access" /&gt;
     &lt;ExcludeApp ID= "Excel" /&gt;
     &lt;ExcludeApp ID= "Groove" /&gt;
     &lt;ExcludeApp ID= "InfoPath" /&gt;
     &lt;ExcludeApp ID= "Outlook" /&gt;
     &lt;ExcludeApp ID= "OneDrive" /&gt;
     &lt;ExcludeApp ID= "Lync" /&gt;
     &lt;ExcludeApp ID= "OneNote" /&gt;
     &lt;ExcludeApp ID= "PowerPoint" /&gt;
     &lt;ExcludeApp ID= "Project" /&gt;
     &lt;ExcludeApp ID= "Publisher" /&gt;
     &lt;ExcludeApp ID= "SharePointDesigner" /&gt;
     &lt;ExcludeApp ID= "Visio" /&gt;
    &lt;/Product&gt;
  &lt;/Add&gt;
&lt;/Configuration&gt;</pre>

  2. run the &#8216;Packager&#8217; switch <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-1783-58" width="1140" height="963" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/07/20160718-140558.mp4?_=58" /><a href="http://theorypc.ca/wp-content/uploads/2016/07/20160718-140558.mp4">http://theorypc.ca/wp-content/uploads/2016/07/20160718-140558.mp4</a></video>
    </div>

  3. This leaves you with a &#8216;blank&#8217; package. Â In reality, it leaves you with a package full of the extra stuff Microsoft crams in. Â The package itself weighs in around 630MB for an AppV office package without office! Â The extra &#8216;configurations&#8217; we can now look at removing.

This is what was left in the workingDir:

<pre class="lang:default decode:true">\WorkingDir\MCXMLs&gt;dir /b /s *.mcxml
\WorkingDir\MCXMLs\en-us\branding.mcxml
\WorkingDir\MCXMLs\en-us\branding_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\DCFMUI.msi.zip_DCFMUI.mcxml
\WorkingDir\MCXMLs\en-us\DCFMUI.msi.zip_DCFMUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\Office64MUI.msi.zip_Office64MUI.mcxml
\WorkingDir\MCXMLs\en-us\Office64MUI.msi.zip_Office64MUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\Office64MUISet.msi.zip_Office64MUISet.mcxml
\WorkingDir\MCXMLs\en-us\Office64MUISet.msi.zip_Office64MUISet_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_AppXManifestLoc.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_AppXManifestLoc_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_OfficeMUI.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_OfficeMUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_PostCommon.Office.MUI.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUI.msi.zip_PostCommon.Office.MUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUISet.msi.zip_OfficeMUISet.mcxml
\WorkingDir\MCXMLs\en-us\OfficeMUISet.msi.zip_OfficeMUISet_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OSMMUI.msi.zip_OSMMUI.mcxml
\WorkingDir\MCXMLs\en-us\OSMMUI.msi.zip_OSMMUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OSMUXMUI.msi.zip_OSMUXMUI.mcxml
\WorkingDir\MCXMLs\en-us\OSMUXMUI.msi.zip_OSMUXMUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\OSMUXMUI.msi.zip_PostCommon.OSMUX.MUI.mcxml
\WorkingDir\MCXMLs\en-us\OSMUXMUI.msi.zip_PostCommon.OSMUX.MUI_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\Proof.Culture.msi.zip_Proof.mcxml
\WorkingDir\MCXMLs\en-us\Proof.Culture.msi.zip_Proof_WoW6432.mcxml
\WorkingDir\MCXMLs\en-us\Proofing.msi.zip_Proofing.mcxml
\WorkingDir\MCXMLs\en-us\Proofing.msi.zip_Proofing_WoW6432.mcxml
\WorkingDir\MCXMLs\es-es\Proof.Culture.msi.zip_Proof.mcxml
\WorkingDir\MCXMLs\es-es\Proof.Culture.msi.zip_Proof_WoW6432.mcxml
\WorkingDir\MCXMLs\fr-fr\Proof.Culture.msi.zip_Proof.mcxml
\WorkingDir\MCXMLs\fr-fr\Proof.Culture.msi.zip_Proof_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\DCF.x-none.msi.zip_MondoWW.mcxml
\WorkingDir\MCXMLs\x-none\DCF.x-none.msi.zip_MondoWW_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_authored.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_authored_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_Common.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_Common_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_licensing.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_licensing_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_MondoWW.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_MondoWW_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_postcommon.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_PostCommon.Office.x-none.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_PostCommon.Office.x-none_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office.x-none.msi.zip_postcommon_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office64WW.msi.zip_crossbitness.mcxml
\WorkingDir\MCXMLs\x-none\Office64WW.msi.zip_crossbitness_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\Office64WW.msi.zip_Office64WW.mcxml
\WorkingDir\MCXMLs\x-none\Office64WW.msi.zip_Office64WW_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\OSM.x-none.msi.zip_MondoWW.mcxml
\WorkingDir\MCXMLs\x-none\OSM.x-none.msi.zip_MondoWW_WoW6432.mcxml
\WorkingDir\MCXMLs\x-none\OSMUX.x-none.msi.zip_MondoWW.mcxml
\WorkingDir\MCXMLs\x-none\OSMUX.x-none.msi.zip_MondoWW_WoW6432.mcxml</pre>

Now explore to &#8220;%TEMP%\Microsoft Office 15\root\mcxml&#8221; and you&#8217;ll see these same mcxml files these extra bits. Â This is where they are pulled to the WorkingDir to be processed. Â If we remove them here then they won&#8217;t be pulled and we can continue without them being installed. Â There are a few files we need to keep as they implement the publishing scripts for the package. Â These files are:

<pre class="lang:default decode:true ">%TEMP%\Microsoft Office 15\root\mcxml\en-us\OfficeMUI.msi.zip_PostCommon.Office.MUI.mcxml
%TEMP%\Microsoft Office 15\root\mcxml\en-us\OfficeMUI.msi.zip_PostCommon.Office.MUI_WoW6432.mcxml
%TEMP%\Microsoft Office 15\root\mcxml\x-none\Office.x-none.msi.zip_authored.mcxml
%TEMP%\Microsoft Office 15\root\mcxml\x-none\Office.x-none.msi.zip_authored_WoW6432.mcxml
%TEMP%\Microsoft Office 15\root\mcxml\x-none\Office64WW.msi.zip_crossbitness.mcxml
%TEMP%\Microsoft Office 15\root\mcxml\x-none\Office64WW.msi.zip_crossbitness_WoW6432.mcxml</pre>

Now modify yourÂ config.xml file and <span style="text-decoration: underline;"><strong>remove</strong> </span>the exclude for the app you want to keep (Lync in my case):

<pre class="lang:xhtml decode:true">&lt;Configuration&gt;
  &lt;Add SourcePath="\\wsapvseq07\d$\Office_2013\2013ProPlus" OfficeClientEdition="32" &gt;
    &lt;Product ID="ProPlusVolume"&gt;
      &lt;Language ID="en-us" /&gt;
     &lt;ExcludeApp ID= "Access" /&gt;
     &lt;ExcludeApp ID= "Excel" /&gt;
     &lt;ExcludeApp ID= "Groove" /&gt;
     &lt;ExcludeApp ID= "InfoPath" /&gt;
     &lt;ExcludeApp ID= "Outlook" /&gt;
     &lt;ExcludeApp ID= "OneNote" /&gt;
     &lt;ExcludeApp ID= "PowerPoint" /&gt;
     &lt;ExcludeApp ID= "Project" /&gt;
     &lt;ExcludeApp ID= "Publisher" /&gt;
     &lt;ExcludeApp ID= "SharePointDesigner" /&gt;
     &lt;ExcludeApp ID= "Visio" /&gt;
    &lt;/Product&gt;
  &lt;/Add&gt;
&lt;/Configuration&gt;</pre>

Rerun the packager command. Â This will ensure your application&#8217;s mcxml file is generated.

Now we want to remove all the previously detected extra files from the %Temp%\Microsoft Office 15\root\mcxmlÂ directory. Â This is what I was left with:

<img class="aligncenter size-full wp-image-1593" src="http://theorypc.ca/wp-content/uploads/2016/07/6.png" alt="6" width="1085" height="708" srcset="http://theorypc.ca/wp-content/uploads/2016/07/6.png 1085w, http://theorypc.ca/wp-content/uploads/2016/07/6-300x196.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/6-768x501.png 768w, http://theorypc.ca/wp-content/uploads/2016/07/6-1024x668.png 1024w" sizes="(max-width: 1085px) 100vw, 1085px" /> 

Now, we do <span style="text-decoration: underline;"><strong>NOT</strong> </span>want to run the setup.exe as that will re-add all the extra bits. Â We just want to run the flattener.exe. Â The command line to do so is:

<pre class="lang:batch decode:true">"%TEMP%\Microsoft Office 15\root\flattener\flattener.exe" /Source:"%TEMP%\Microsoft Office 15" /Dest:"D:\Office_2013\Lync_2013" /Version:"15.0.4833.1001" /ProductReleaseIDs:"ProPlusVolume" /Cultures:"en-us,x-none" /Platform:"x86"</pre>

Now we have a package that is only 62MB in size

<img class="aligncenter size-full wp-image-1594" src="http://theorypc.ca/wp-content/uploads/2016/07/8.png" alt="8" width="808" height="366" srcset="http://theorypc.ca/wp-content/uploads/2016/07/8.png 808w, http://theorypc.ca/wp-content/uploads/2016/07/8-300x136.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/8-768x348.png 768w" sizes="(max-width: 808px) 100vw, 808px" /> 

And if we open it in AppV we see only the components we are really interested in, just Skype For Business and we don&#8217;t have any other junk. Â At this stage, the package &#8216;fails&#8217; because some of the dependencies are not in the package. Â I hope to be exploring what the exact dependencies are in a future post to explore what the actual \*minimum\* package size we can get away with for some of these Office applications.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->