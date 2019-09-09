---
id: 554
title: Citirx Universal Print Server - More Zebra label printer troubleshooting tips
date: 2015-07-09T15:37:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/09/citirx-universal-print-server-more-zebra-label-printer-troubleshooting-tips/
permalink: /2015/07/09/citirx-universal-print-server-more-zebra-label-printer-troubleshooting-tips/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/citirx-universal-print-server-more.html
blogger_internal:
  - /feeds/7977773/posts/default/2041610078319935112
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
---
Continuing on [my previous post](http://trentent.blogspot.ca/2015/07/citrix-universal-print-server.html) I was troubleshooting some issues with some Zebra label printers.  I thought I had it working by switching the rendering path to XPS.  This did allow the printers to print - or so I thought.  It turns out that some of the older Zebra printers we had (LP-2824) were not printing the labels correctly, a newer LP-2824 PLUS was printing correctly.  The LP-2824's were scaling the labels down to 20% for some reason.

In order to capture this so I wasn't wasting legions of paper, you need to turn on 'Keep printed documents' in the 'Advanced' tab of your printer.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-3Mhr4h092HA/VZ7gsdcaDvI/AAAAAAAAA9E/VzRwIEYcO3U/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B2.58.30%2BPM.png"><img src="http://3.bp.blogspot.com/-3Mhr4h092HA/VZ7gsdcaDvI/AAAAAAAAA9E/VzRwIEYcO3U/s320/Screen%2BShot%2B2015-07-09%2Bat%2B2.58.30%2BPM.png" width="284" height="320" border="0" /></a>
</div>

On your print server (not the client), this option will store the spooler files (SPL) here:  
C:\windows\system32\spool\printers

Generally, there are two types of formats encapsulated in a SPL file, RAW and EMF.  Citrix provides a EMF reader in 'cpviewer.exe'.  This utility is located in Program Files (x86) and is used like this:  
"C:\Program Files (x86)\Citrix\ICA Client\cpviewer.exe" C:\00167.SPL

This will bring up a preview.

<div>
</div>

I setup two scenarios for printing.

Scenario 1 - Printing directly to the print queue with the "ZDesigner LP 2824" driver  
Scenario 2 - Printing through the Citrix Universal Print Driver via UPS.

Trying both scenarios caused my cpviewer.exe to hang/freeze.  This is where I learned the cpviewer utility ONLY works with EMF-type SPL files.  Trying to read a RAW file will cause it to hang.  I was able to verify it was RAW by looking at the preferences for the print driver:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-t9k7jPqTgZY/VZ7jpxL5YOI/AAAAAAAAA9Q/YCDg_0KBO7k/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B3.10.56%2BPM.png"><img src="http://1.bp.blogspot.com/-t9k7jPqTgZY/VZ7jpxL5YOI/AAAAAAAAA9Q/YCDg_0KBO7k/s320/Screen%2BShot%2B2015-07-09%2Bat%2B3.10.56%2BPM.png" width="280" height="320" border="0" /></a>
</div>

&nbsp;

<div>
  It turns out that 'Printer default' and 'Raw' are the same value.  Selecting 'Enhanced metafile' and reprinting to that printer created the proper SPL file that I could open in cpviewer.  With this I could now see the issue the users were reporting:
</div>

<div>
</div>

<div>
  <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
    <tr>
      <td style="text-align: center;">
        <a style="margin-left: auto; margin-right: auto;" href="http://1.bp.blogspot.com/-rM1tPcjTd78/VZ7mFyLyQwI/AAAAAAAAA9k/0uTX5YRuuf0/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B2.56.41%2BPM.png"><img src="http://1.bp.blogspot.com/-rM1tPcjTd78/VZ7mFyLyQwI/AAAAAAAAA9k/0uTX5YRuuf0/s320/Screen%2BShot%2B2015-07-09%2Bat%2B2.56.41%2BPM.png" width="320" height="123" border="0" /></a>
      </td>
    </tr>
    
    <tr>
      <td style="text-align: center;">
        Left print is direct to the printer, on the right is through Citrix UPD.  I'm missing the barcode on the right.
      </td>
    </tr>
  </table>
</div>

<div>
  With the ZDesigner driver, if I forced the 'Enhanced metafile' in the preferences, all the Citrix UPD prints lost their barcodes.  If I set the Citrix UPD to XPS and the Zebra on RAW it printed with a barcode, but the scale was still horribly off (same as the picture above).
</div>

<div>
</div>

<div>
  I tried adjusting almost every option available on the ZDesigner side, from the paper size, dpi, different stocks, etc.  It consistently turned out scaled down.  If I adjusted the margins it would move the graphic around but nothing would enable the graphic to be the correct size.  I looked at the <a href="http://support.citrix.com/article/CTX119690">Citrix UPD advanced settings</a> and changed the paper height, width, dpi, scale, print quality, orientation, etc. Not a single affect was made except for orientation.  At least I knew the settings were applying because of that.  Orientation didn't make a difference in the scale, just made the tiny graphic vertical instead of horizontal.  At this point I decided it had to be driver related.  I downloaded two different sets of ZDesigner Zebra drivers for the LP-2824, the latest and the last major version release, installed both but the exact same issue still persisted.
</div>

<div>
</div>

<div>
  Searching for others that may have had the same issue or something similar I found a link to another company providing <a href="http://www.seagullscientific.com/drivers/printer-driver-features.aspx?m=Zebra+LP2844">Zebra label printer drivers, Seagull Scientific</a>.
</div>

<div>
</div>

<div>
  With nothing else to lose, I installed and configured those drivers per the original spec for media, etc.  Then I printed and checked the SPL file:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-NaQogI_sbM4/VZ7o7PnFvKI/AAAAAAAAA9w/N2chkMUL8gI/s1600/Screen%2BShot%2B2015-07-09%2Bat%2B3.33.41%2BPM.png"><img src="http://4.bp.blogspot.com/-NaQogI_sbM4/VZ7o7PnFvKI/AAAAAAAAA9w/N2chkMUL8gI/s320/Screen%2BShot%2B2015-07-09%2Bat%2B3.33.41%2BPM.png" width="320" height="198" border="0" /></a>
</div>

<div>
</div>

<div>
</div>

<div>
  Success!  Barcode is present!  Scale is correct!  Format is pure EMF from Citrix UPD to barcode printer, no XPS anywhere, no RAW anywhere!
</div>

<div>
</div>

<div>
  All that said, this is a ringing endorsement for what Seagull Scientific has done with the Zebra Barcode printers.  Their drivers *just work* as opposed to the ZDesigner drivers.  If you need to print to older Zebra printers, heck, probably for all Zebra printers, use Seagull's drivers.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->