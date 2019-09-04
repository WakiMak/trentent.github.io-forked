---
id: 2883
title: 'Printer names show up as GUID&#8217;s in Event Viewer'
date: 2018-12-03T13:32:28-06:00
author: trententtye
layout: revision
guid: https://theorypc.ca/2018/12/03/2877-revision-v1/
permalink: /2018/12/03/2877-revision-v1/
---
I extended a ControlUp SBA to look into the duration that printers take to map into a Citrix session.Â While working on this and touting the advantages to Guy Leech, Guy made mention that he was only seeing GUID&#8217;s and not sane name.<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/GUIDName.png" alt="" class="wp-image-2878" srcset="http://theorypc.ca/wp-content/uploads/2018/12/GUIDName.png 867w, http://theorypc.ca/wp-content/uploads/2018/12/GUIDName-300x197.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/GUIDName-768x505.png 768w" sizes="(max-width: 867px) 100vw, 867px" /> <figcaption>What printer does this GUID refer to?</figcaption></figure> <figure class="wp-block-image"><img src="http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1.png" alt="" class="wp-image-2880" srcset="http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1.png 858w, http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1-246x300.png 246w, http://theorypc.ca/wp-content/uploads/2018/12/SaneName-1-768x936.png 768w" sizes="(max-width: 858px) 100vw, 858px" /><figcaption>This makes a little more sense&#8230;</figcaption></figure> 

[Doing some google searches](https://social.technet.microsoft.com/Forums/en-US/d2c12fc3-8f09-4c8f-9f6b-bee92fff81fa/printer-guid-logged-in-operational-log?forum=winserverprint) shows this [seems to be a common issue](https://social.technet.microsoft.com/Forums/en-US/cae24bc5-406a-46ec-9fea-4408b170ea10/resolve-eventid-306-printer-name?forum=winserverprint).

So I started to experiment to see if I could find a pattern for why the GUID was appearing sometimes.

What I believe is occuring: the print server has a local driver installed and that driver is _preferred_.Â When the mapped printer is connected to the server it associates the printer via the GUID name instead.

If the printer does NOT have a local driver installed or the server &#8220;maps&#8221; an alternative driver (eg, the Citrix Universal Print Driver) to the printer than the sane name comes through.Â 

I was able to test this hypothesis by setting the Citrix Universal Printing policies and here were the results:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/Testing.png" alt="" class="wp-image-2881" srcset="http://theorypc.ca/wp-content/uploads/2018/12/Testing.png 1980w, http://theorypc.ca/wp-content/uploads/2018/12/Testing-300x34.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/Testing-768x87.png 768w, http://theorypc.ca/wp-content/uploads/2018/12/Testing-1600x182.png 1600w" sizes="(max-width: 1980px) 100vw, 1980px" /> </figure> 

Now, it&#8217;s possible that Citrix is doing some magic and causing the name to be sane instead.Â I didn&#8217;t have an opportunity to map a different set of printer drivers via another set of technologies, but the hypothesis seems to hold for now.

Is there a way to map the GUID to a real printer?

Yes.

The GUID&#8217;s themselves reference specific printers stored under the users registry hive:<figure class="wp-block-image">

<img src="http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers.png" alt="" class="wp-image-2882" srcset="http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers.png 1514w, http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers-300x105.png 300w, http://theorypc.ca/wp-content/uploads/2018/12/HKCU_Printers-768x269.png 768w" sizes="(max-width: 1514px) 100vw, 1514px" /> </figure> 

Since you can&#8217;t have backslashes in the names of registry keys, Microsoft has done a simple replace with comma&#8217;s (&#8220;,&#8221;).Â And now you can correlate the event to the printer!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->