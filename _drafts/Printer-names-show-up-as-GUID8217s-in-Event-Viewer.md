---
id: 2877
title: 'Printer names show up as GUID&#8217;s in Event Viewer'
date: 2018-12-06T10:15:43-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2877
permalink: /?p=2877
categories:
  - Uncategorized
---
I extended a ControlUp SBA to look into the duration that printers take to map into a Citrix session.Â While working on this and touting the advantages to [Guy Leech](https://twitter.com/guyrleech), Guy made mention that he was only seeing GUID&#8217;s and not sane name.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/GUIDName.png" alt="" class="wp-image-2878" srcset="http://theorypc.ca/wp-content/uploads/2018/12/GUIDName.png 867w, http://theorypc.ca/wp-content/uploads/2018/12/GUIDName-300x197.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/GUIDName-768x505.png 768w" sizes="(max-width: 867px) 100vw, 867px" /> <figcaption>What printer does this GUID refer to?</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1.png" alt="" class="wp-image-2880" srcset="http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1.png 858w, http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1-246x300.png 246w, http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1-768x936.png 768w" sizes="(max-width: 858px) 100vw, 858px" /><figcaption>This makes a little more sense&#8230;</figcaption></figure> 

[Doing some google searches](https://social.technet.microsoft.com/Forums/en-US/d2c12fc3-8f09-4c8f-9f6b-bee92fff81fa/printer-guid-logged-in-operational-log?forum=winserverprint) shows this [seems to be a common issue](https://social.technet.microsoft.com/Forums/en-US/cae24bc5-406a-46ec-9fea-4408b170ea10/resolve-eventid-306-printer-name?forum=winserverprint).

So I started to experiment to see if I could find a pattern for why the GUID was appearing sometimes.

Citrix has two methods of mapping printers.Â One is via a &#8220;direct connection&#8221; and the other is Citrix Printer Mapping.Â When direct connection is enabled, any printers that are mapped to printer servers have that path \*installed\* on the server.Â Depending on your &#8220;universal print driver usage&#8221; policy, this could mean drivers will get installed on your Citrix servers!

How do you determine what could be a direct connection printer?Â A direct connection printer can be setup by simply browsing to your print server and &#8220;Connect&#8230;&#8221;.<figure class="wp-block-image is-resized">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinterFromServer.png" alt="" class="wp-image-2884" width="446" height="187" srcset="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinterFromServer.png 877w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinterFromServer-300x126.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinterFromServer-768x322.png 768w" sizes="(max-width: 446px) 100vw, 446px" /> </figure> 

These devices show up on your local device as &#8220;%printerName% on %printServer%&#8221;Â <figure class="wp-block-image is-resized">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinter.png" alt="" class="wp-image-2885" width="544" height="311" srcset="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinter.png 637w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPrinter-300x171.png 300w" sizes="(max-width: 544px) 100vw, 544px" /> <figcaption>Highlighted are printers on my Windows 10 that can be &#8220;direct connection&#8221; printers</figcaption></figure> 

The other printers are local queues and will be mapped via Citrix Printer Mapping.

To enable &#8220;Direct Connection&#8221;, Citrix has a policy &#8220;Direct connection to print servers&#8221;.Â By default, this policy is enabled.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy.png" alt="" class="wp-image-2886" srcset="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy.png 1135w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy-300x19.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy-768x47.png 768w" sizes="(max-width: 1135px) 100vw, 1135px" /> </figure> 

With this policy enabled, it changes the behavior of how Citrix connects printers these &#8220;direct connection&#8221; printers.Â Citrix has a diagram here:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1.png" alt="" class="wp-image-2890" srcset="http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1.png 496w, http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1-300x165.png 300w" sizes="(max-width: 496px) 100vw, 496px" /> </figure> 

But I feel this is missing temporal information.Â I&#8217;ve created a video to highlight the steps.<figure class="wp-block-video"><video controls src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionProcess.mp4"></video><figcaption>Direct Connection Process</figcaption></figure> 

Why is this important?

Citrix has an option that is an absolute requirement for some applications.Â Â  
**&#8220;Wait for printers to be created (server desktop)**&#8220;.

This policy needs to be enabled for numerous applications that require a specific printer is there and available before the app starts.Â This is usually due to applications that have pre-defined printers like label printers and do a check on app launch.Â If you have an application like that you have probably enabled this feature in the past.

## Citrix has many options for managing printers and they can impact your logon process.

Focusing on if Direct Connection is enabled, and if &#8220;**Wait for printers to be created (server desktop)**&#8221; policy is also <span style="text-decoration: underline;"><strong>enabled</strong></span>, care MUST be taken to minimize logon times.

Direct connection does two things that can have a very adverse affect on logon time that is set by default.Â When it connects directly to the print server it will check and _**install**_ the print driver on the Citrix server.Â The installation activity is visible via process monitor.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading.png" alt="" class="wp-image-2893" srcset="http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading.png 1655w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-300x47.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-768x120.png 768w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-1600x249.png 1600w" sizes="(max-width: 1655px) 100vw, 1655px" /> <figcaption>The DrvInst.exe process is the installation of printer drivers</figcaption></figure> 

The installation of printer driver

Now, it&#8217;s possible that Citrix is doing some magic and causing the name to be sane instead.Â I didn&#8217;t have an opportunity to map a different set of printer drivers via another set of technologies, but the hypothesis seems to hold for now.

Is there a way to map the GUID to a real printer?

Yes.

The GUID&#8217;s themselves reference specific printers stored under the users registry hive:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers.png" alt="" class="wp-image-2882" srcset="http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers.png 1514w, http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers-300x105.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers-768x269.png 768w" sizes="(max-width: 1514px) 100vw, 1514px" /> </figure> 

Since you can&#8217;t have backslashes in the names of registry keys, Microsoft has done a simple replace with comma&#8217;s (&#8220;,&#8221;).Â And now you can correlate the event to the printer!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->