---
id: 2895
title: Printers and their impact on logon duration
date: 2019-03-05T11:06:00-06:00
author: trententtye
excerpt: Improve logon duration by find out about how long printers take to add to a session.
layout: post
guid: http://theorypc.ca/?p=2895
permalink: /2019/03/05/printers-and-their-impact-on-logon-duration/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionProcess.mp4
    197
    video/mp4
    
image: /wp-content/uploads/2018/12/FeaturedImage.png
categories:
  - Blog
tags:
  - 2008R2
  - 2012R2
  - "2016"
  - Citrix
  - ControlUp
  - Group Policy
  - Logons
  - Performance
  - PowerShell
  - printing
  - scripting
  - &
  - XenDesktop
---
40 second logons.&nbsp; 100 second logons.&nbsp; 200 second logons.&nbsp;&nbsp;

Our users had an average logon time of 8-11 seconds.&nbsp; But some users were hitting 40-200 seconds.&nbsp; Users were frustrated and calling the help desk.

Why?

I did not want to individually examine all of the users whom had a long logon duration.&nbsp; There was dozens of dozens of users, but after sampling 5 of them, the root cause was consistent.&nbsp; Printers were causing long logons.&nbsp;&nbsp;

What I found was users were encountering the long logons on specific workstations.&nbsp; Further investigation revealed these workstations had globally mapped printers.&nbsp; Some workstations had dozens of printers.&nbsp; Some of the printers were long gone, having been moved/re-IP'ed, shutdown, or re-provisioned and the entry in the workstation not updated.&nbsp; Citrix, depending on Receiver version, attempts to do a check to validate the printer's operation before mapping and this would wait for a network timeout (or a crash of Receiver).&nbsp; When I stopped the print spooler on the local workstation, logons came back down to the proper range.

What I sought to do was create something to make identifying this root cause easier, quicker, and with more specificity - in other words was there a particular printer causing the delay?

## Citrix Printing Methods

Citrix has two methods of printing.&nbsp; "Direct connection" and local printing.&nbsp; Direct connection has the potential for larger impact on logon duration as it executes additional steps and actions on the Citrix server.

To enable "Direct Connection", Citrix has a policy "Direct connection to print servers".&nbsp; **By default, this policy is <span style="text-decoration: underline;">enabled</span>**.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy.png" alt="" class="wp-image-2886" srcset="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy.png 1135w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy-300x19.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionPolicy-768x47.png 768w" sizes="(max-width: 1135px) 100vw, 1135px" /> </figure> 

With this policy enabled, it changes the behavior of how Citrix connects printers you have mapped locally as network printers.&nbsp; Citrix has a diagram here:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1.png" alt="" class="wp-image-2890" srcset="http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1.png 496w, http://theorypc.ca/wp-content/uploads/2018/12/cds-print-network-attached-1-300x165.png 300w" sizes="(max-width: 496px) 100vw, 496px" /> </figure> 

But I feel this is missing temporal information.&nbsp; I've created a video to highlight the steps.<figure class="wp-block-video"><video controls src="http://theorypc.ca/wp-content/uploads/2018/12/DirectConnectionProcess.mp4"></video><figcaption>Direct Connection Process</figcaption></figure> 

## Why is this important?

Citrix has an option that is an absolute requirement for some applications.&nbsp;&nbsp;  
**"Wait for printers to be created (server desktop)**" (_Virtual Apps and Desktops 7.x_) or "**Start this application without waiting for printers to be created. (Unchecked)**" (_Citrix & 6.5_).

This policy needs to be enabled for numerous applications that require a specific printer is present before the application starts.&nbsp; This is usually due to applications that have pre-defined printers like label printers and do a check on app launch.&nbsp; If you have an application like that you probably&nbsp; have this feature enabled.

## Citrix has many options for managing printers and they can impact your logon process.

Focusing on Direct Connection being enabled, if "**Wait for printers to be created (server desktop)**" policy is also <span style="text-decoration: underline;"><strong>enabled</strong></span>, care MUST be taken to minimize logon times.

Direct connection does something that can have a very adverse affect on logon time that is **enabled** by default.&nbsp; When it connects directly to the print server it will check and _**install**_ the print driver on the Citrix server.&nbsp; The installation activity is visible via process monitor.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading.png" alt="" class="wp-image-2893" srcset="http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading.png 1655w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-300x47.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-768x120.png 768w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterDriverLoading-1600x249.png 1600w" sizes="(max-width: 1655px) 100vw, 1655px" /> <figcaption>The DrvInst.exe process is the installation of printer drivers</figcaption></figure> 

The installation of printer driver, **by default**, will only occur if the driver is inbox or prestaged on the Citrix server.&nbsp; But the check to see if a print driver needs to be installed occurs every time a session is created.&nbsp; This behavior can be changed by policy.

Why does this impact logon times?&nbsp; Driver installation can take a while, especially if there is communication issues between the Citrix server and print server.&nbsp; Or if the print server is under stress and just simply slow to respond.&nbsp; Or if the driver package that needs to be loaded is especially large, has lots of forms or page formats, multiple trays, etc.&nbsp; This all adds up.

### How can I tell what the impact of printers might be on Logon Duration?<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/LogonDurationOther.png" alt="" class="wp-image-2899" srcset="http://theorypc.ca/wp-content/uploads/2018/12/LogonDurationOther.png 765w, http://theorypc.ca/wp-content/uploads/2018/12/LogonDurationOther-300x48.png 300w" sizes="(max-width: 765px) 100vw, 765px" /> </figure> 

I've been working on updating a Script-Based Action (SBA) originally posted by [Guy Leech](https://www.linkedin.com/in/guyrleech/) on the [Citrix blogs](https://www.citrix.com/blogs/2018/10/10/analyze-logon-duration-script-just-got-more-powerful/).&nbsp; This enhancement to the SBA will output more information breaking down what's consuming your logon times.&nbsp; Specifically, this tackles how long printers take in the [ControlUp](https://www.controlup.com/) column: "Logon Duration - Other"  


I've created a video showing how to enable the additional logging and how to run the new Script Based Action and what the output looks like:<figure class="wp-block-embed-youtube wp-block-embed is-type-video is-provider-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio">

<div class="wp-block-embed__wrapper">
</div></figure> 

### What's new in the output?<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/PrinterLoadTimesOutput.png" alt="" class="wp-image-2898" srcset="http://theorypc.ca/wp-content/uploads/2018/12/PrinterLoadTimesOutput.png 1069w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterLoadTimesOutput-300x24.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/PrinterLoadTimesOutput-768x60.png 768w" sizes="(max-width: 1069px) 100vw, 1069px" /> </figure> 

For the individual printers, direct connection printers now show two lines, the&nbsp;**Driver&nbsp;**load time and **Printer**&nbsp;load/connection time.&nbsp; 

The driver load time is titled with **Driver** and then the UNC path to the printer.&nbsp; 

The&nbsp;**Printer** and UNC path shows&nbsp;the the actual connection, printer configuration and establishment time.&nbsp; All Direct Connection printers will be shown with <span style="text-decoration: underline;"><strong>UNC</strong> </span>paths.

Citrix Universal Print Driver (UPD) mapped printers using the Citrix Universal Print Driver (print jobs that get sent back to the client for processing) are shown with their "Friendly Name" and do not have a matching "Driver" component as no driver loading is required.&nbsp; In the example output above, **"\\printsrv\HP Color LaserJet 5550"** is a direct connection printer and **"Zebra R110Xi HF (300 dpi)"** is a Citrix UPD printer.

Here is an example of the full output:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2019/03/FullOutput.png" alt="" class="wp-image-2964" srcset="http://theorypc.ca/wp-content/uploads/2019/03/FullOutput.png 872w, http://theorypc.ca/wp-content/uploads/2019/03/FullOutput-300x139.png 300w, http://theorypc.ca/wp-content/uploads/2019/03/FullOutput-768x355.png 768w" sizes="(max-width: 872px) 100vw, 872px" /> </figure> 

The "**Connect to Printers**" is a sub-phase under "**Pre-Shell (Userinit)**".&nbsp; Each "**Printer:**" and "**Driver:**" is a sub-phase under "**Connect to Printers**".&nbsp; In this example screenshot, "**Pre-Shell (Userinit)**" was consuming 117.6 seconds of a 133.2 second logon duration.&nbsp; Prior to this script that was all that was known.&nbsp; Now we can see that connecting to printers consumes 97.7 of those 117.6 seconds!

## Awesome!&nbsp; What's the catch?

In order to generate this information, some more verbose logging is required on the Citrix server side.&nbsp; &nbsp;I specifically worked to ensure that no 3rd party utilities would be required so we simply need to enable 4 additional features.

Command Line Audit  
PrintServer/Operational Logs  
Audit Process Creation/Termination

I've written a batch script to enable these features:

<pre class="wp-block-preformatted">REM Enable Command Line Auditing
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /d 0x1

REM Enable Print Service logging, no retention, size 500MB
wevtutil sl Microsoft-Windows-PrintService/Operational /ms:524288000 /rt:false /ab:false /e

REM Enable Process Termination
Auditpol /set /subcategory:"Process Termination" /success:enable

REM Enable Process Creation
Auditpol /set /subcategory:"Process Creation" /success:enable
</pre>

Easy!

### Wait a minute!&nbsp; Windows Server 2008R2 doesn't have Command Line Auditing!

Good catch!&nbsp; Windows Server 2008R2 doesn't have command line auditing so it will miss the driver load information.&nbsp; There is no way around this using native tools, but modification could be done to use a 3rd party tool like sysmon.&nbsp; The output on a 2008R2 system would show the following (with all other features enabled).<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2019/03/2008R2_output-1.png" alt="" class="wp-image-2963" srcset="http://theorypc.ca/wp-content/uploads/2019/03/2008R2_output-1.png 857w, http://theorypc.ca/wp-content/uploads/2019/03/2008R2_output-1-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2019/03/2008R2_output-1-768x321.png 768w" sizes="(max-width: 857px) 100vw, 857px" /> </figure> 

## Awesome!&nbsp; Let me at this script!

This script is [available on the ControlUp site here](https://www.controlup.com/script-library/Analyze-Logon-Duration/b636f434-2843-4a5c-a21a-eed7a02d0946/).&nbsp; If you have ControlUp you can run simply add it via the "Script Based Actions" button and further derive more information on your users logon performance!&nbsp;&nbsp;

## Last Gotcha

In some instances the "Connect to Printers phase" may start before "Pre-Shell". This has been observed and is actually happening! The operating system is attempting to start \*some\* printer events asynchronously in certain situations and so the connect to Printers may start before userinit, but overall connecting to printers may still be blocking logons while it trys and completes.

[Steve Elgan](https://twitter.com/selgan) kindly pointed out that it's important you size your event logs to ensure the data is present for the users whom you want to monitor. If your event logs are sized too small than the data being queried by the script won't be present. So ensure you have your Security and the Printer event logs sized large enough to capture all of this data! For an idea of scale, you can look at your log size and the oldest entry there. If your log is 1MB in size and your oldest entry is from an hour ago, than sizing your log to 24MB should capture about a day's worth of information.



Hope this helps you reduce your long logon durations!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->