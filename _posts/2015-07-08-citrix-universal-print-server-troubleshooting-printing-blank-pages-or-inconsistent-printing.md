---
id: 555
title: Citrix Universal Print Server - Troubleshooting printing blank pages or inconsistent printing.
date: 2015-07-08T23:45:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/08/citrix-universal-print-server-troubleshooting-printing-blank-pages-or-inconsistent-printing/
permalink: /2015/07/08/citrix-universal-print-server-troubleshooting-printing-blank-pages-or-inconsistent-printing/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/citrix-universal-print-server.html
blogger_internal:
  - /feeds/7977773/posts/default/8270908189123400457
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
---
We have an application that is hard coded to map printers via a UNC path. &nbsp;This is the bane of a Citrix admin whom wants to minimize the number of drivers on The XenApp Server as each UNC connection can prompt for a driver install (this is how our environment is configured). &nbsp;User's click 'Install Driver' and boom, your Citrix server has another driver on your server and another point of possible instability.

Citrix has attempted to solve this using the Universal Print Driver (UPD) but this just maps printers from your local system to the Citrix session. &nbsp;Each printer is given a unique name and each queue is given a unique port as well.

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-XMdJm57Bakg/VZ3aLfX0r4I/AAAAAAAAA6I/DzuDNtkavkY/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B8.11.38%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://1.bp.blogspot.com/-XMdJm57Bakg/VZ3aLfX0r4I/AAAAAAAAA6I/DzuDNtkavkY/s320/Screen%2BShot%2B2015-07-08%2Bat%2B8.11.38%2BPM.png" width="306" /></a>
</div>



<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-h5ELEzcdSJI/VZ3aLcOhHOI/AAAAAAAAA6M/bmdLnBLu7VM/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B8.12.27%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="159" src="http://1.bp.blogspot.com/-h5ELEzcdSJI/VZ3aLcOhHOI/AAAAAAAAA6M/bmdLnBLu7VM/s320/Screen%2BShot%2B2015-07-08%2Bat%2B8.12.27%2BPM.png" width="320" /></a>
</div>

Unfortunately, this makes it impossible for our hardcoded app to use a consistent printer as these queues and names do not exist unless we install them locally. &nbsp;If the program displayed a simple print dialog this wouldn't be an issue but it is not coded that way.

Fortunately, Citrix has come up with a \*fairly\* elegant solution: Citrix Universal Print Server (UPS). &nbsp;How this works is it forces the mapped network printers to come across using the Citrix Universal Print Driver. &nbsp;There are two parts to this, the Citrix UPS and the Universal Print Client (UPC). &nbsp;The UPS goes on your Windows Print Server and the UPC goes on your XenApp servers.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-MiSoe9gFTiQ/VZ3b5zfQCiI/AAAAAAAAA6c/APklLefHnWc/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B8.25.17%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="23" src="http://2.bp.blogspot.com/-MiSoe9gFTiQ/VZ3b5zfQCiI/AAAAAAAAA6c/APklLefHnWc/s320/Screen%2BShot%2B2015-07-08%2Bat%2B8.25.17%2BPM.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      XenApp server
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-jcWCaBGgSok/VZ3b55oOjBI/AAAAAAAAA6g/tifpZcMnCCg/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B8.26.09%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="18" src="http://3.bp.blogspot.com/-jcWCaBGgSok/VZ3b55oOjBI/AAAAAAAAA6g/tifpZcMnCCg/s320/Screen%2BShot%2B2015-07-08%2Bat%2B8.26.09%2BPM.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Windows Print Server
    </td>
  </tr>
</table>

To enable the UPS functionality and have your network printers use the Citrix UPD you need to enable a Citrix group policy object on The XenApp Server (that's why you see Citrix Group Policy Management (x64) 1.7.0.0). &nbsp;If you have a version older than 1.7.0.0 then you won't have the relevant policy available to you:

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-5PnZeJjjWm4/VZ3c3JcuilI/AAAAAAAAA6s/-4nYQRv7X2s/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B8.30.25%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="456" src="http://3.bp.blogspot.com/-5PnZeJjjWm4/VZ3c3JcuilI/AAAAAAAAA6s/-4nYQRv7X2s/s320/Screen%2BShot%2B2015-07-08%2Bat%2B8.30.25%2BPM.png" width="640" /></a>
</div>

Setting this setting to either "Enabled" setting turns on the UPC feature. &nbsp;For any network printer that you map to the Windows Print Server with the UPS it will use the Citrix UPD. &nbsp;To verify this, go to your XenApp server and map a printer from the UPS and look at the driver.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-EWSc-dHnzCA/VZ3j1xbzEmI/AAAAAAAAA68/yVLyAfBay1Q/s1600/UPS_In_Action.gif" style="margin-left: auto; margin-right: auto;"><img border="0" src="http://3.bp.blogspot.com/-EWSc-dHnzCA/VZ3j1xbzEmI/AAAAAAAAA68/yVLyAfBay1Q/s1600/UPS_In_Action.gif" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      UPS in Action. &nbsp;Note the driver for the network printer is "Citrix Universal Printer"
    </td>
  </tr>
</table>

Ok, so with UPS installed and working we should be good right?

Right? &nbsp;

Well... &nbsp;It turns out that our label printers were printing out blanks with Citrix UPS. &nbsp;To determine if it was truly the UPS causing my problem I enabled printer mapping, added the network printer locally complete with the native driver and launched my app. &nbsp;This mapped my local printer into my session with the Citrix UPD. &nbsp;I tried printing and... &nbsp; nothing. &nbsp;Just a blank label came out.

To troubleshoot this process, Zebra actually has a good document on determining if your printer is rendering/printing correctly by [printing to a text file](https://km.zebra.com/kb/index?page=content&id=SO6873). &nbsp;If you have a new enough Zebra printer installed you can [actually use it to 'preview' the label](https://km.zebra.com/kb/index?page=content&id=SO8374&actp=LIST) so you don't waste paper, AND you don't need have a label printer physically present beside you. &nbsp;Older Zebra's don't seem to have this functionality (LP 2824 I'm looking at you!). &nbsp;I had a PDF file I tried printing from PDF Architect that output to the text file.

So, what did my print job look like?

> <span style="font-family: Courier New, Courier, monospace;">CT~~CD,~CC^~CT~<br />^XA<br />^PW445<br />^PQ1,0,1,Y^XZ</span>

<div style="-webkit-text-stroke-color: initial; -webkit-text-stroke-width: 0.10000000149011612px; color: #1b1c0a; font-family: arial, helvetica, sans-serif; font-size: 14px; line-height: 22px; margin: 0px; padding: 0px;">
</div>

What does this look like on the Zebra?



<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-VRXxPOpOkVk/VZ3p8XiaypI/AAAAAAAAA7M/Qk1E9ByhHjg/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B9.25.57%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://2.bp.blogspot.com/-VRXxPOpOkVk/VZ3p8XiaypI/AAAAAAAAA7M/Qk1E9ByhHjg/s320/Screen%2BShot%2B2015-07-08%2Bat%2B9.25.57%2BPM.png" width="292" /></a>
</div>

<div style="-webkit-text-stroke-color: initial; -webkit-text-stroke-width: 0.10000000149011612px; color: #1b1c0a; font-family: arial, helvetica, sans-serif; font-size: 14px; line-height: 22px; margin: 0px; padding: 0px;">
  <span style="-webkit-text-stroke-width: 0px; color: black; font-family: Times; font-size: xx-small; line-height: normal;"><br /></span>
</div>

Well then. Curiously, if I printed the same document from Adobe Reader it came out like this:

> <span style="font-family: Courier New, Courier, monospace;">CT~~CD,~CC^~CT~<br />^XA<br />^PW446<br />^FO0,0^GFA,23296,23296,00056,:Z64:<br />eJztmrFuE0EQhvecCw4oIha6woQliUQELUJISekCUacIissAViJRWY4TWxQoQhSUKShc5jFc5jGucBHxCFQnFNnsnk1zV/jmMzqFaP4uxaeZnd1/Z24dY1QqlUqlUqlUKpVKpVKpVCqVSqVSqVQqlUqlUqlUKpVKdTu1OfmrsYirmdCYuvlgTOXuck3IJZCLIdcnXAvm2YXcNtyHLq8LirdAnohrQe51yXlWJxN6Xv6LfSib2y//nKH7jPqd+q8F/b5Anuj+7MJ6Ot8OS+4rBfbBQo7GK5uj63uY+Xs2L/2cxz2AXDbeLM8vsC6fbxuX24dlGG8p5Tqlre99MS63f28h14PcXjFuDca7n+U+Om4dxHvluE1cl7n7nuMOHbcxP956ltsplmeU5bopNzfPHNd23NP53EaWewn7w1Ho+5HcDzvp95GcO3HcZPJbzFWhbyuwLv6cDcwntD6S5yPIncF7sA3j7X1jHI3n9+Ex2L/lljeJvC6dN4X8nuOOt9n6trqF7okc5+vyBMSLit3XOe6wxerS6bK6HKd1kfuv576r7DWcXy7h/Hkt5ui722LvdWO4vq9yrg25PcjRPBdZX3xD+4qcewf7mMuzTr43z+D32EFobCz3Q+00NFEC/NeD77SuLjZpyDk3h6wCv/tztpIAbs3l2Qe+dfUMof/qCfORjQH33O8D4M5gPH9e+sC3Dei/e5B7Af3Xg9zJ7Xy3yaroO8q/ircYx+6JMucQz9UhZ+vg/nRcVPIcYiG3Sriau+fhvUvzDMkcssXnAkve59N+y+5BS/x36ucCcK4PfJ8G/uu4c70L8nTzhB0CH634ejLfrpK69Hw84Nt96IdncC7o+P4OONenwyHzH5qXGv5cA9/6OZL4YQv6qA37XxvGS/sR41BdDM/zLs8Fi81ZbC6A7xPBFfzuP2ccjReMYJ4RXR+ZC1yeDfI+4bgY+uGc/a4djODv6D9Y3wyumsx/aO7xfZr5KBjA9Y3IXODPC5kL0vcQ6CPSp128EXzX+E7eGXyeZA5x8RoJ8+2A+c/dZ6VzJf8/A/k9oOr7n/x3yin3q7w8034LubH895XpdzjlWJ7Mf8YMWDwL86zFN2x9sD/YJvz/l0Tso2DWx6Tc8oXn+uJzvZRyyaWUC2f/ty3lqtP1iX2UxouG4vVN6ynnQl+XKBmzejbZuY4SMVdJ17cr9m1al3oMfQv7u4lvjsg+AB+Zi2kfk96fFdj/Av9OhDiLOFcX1DeXLiz8np5y0v1TqQroD3Suisg=:DA24<br />^PQ1,0,1,Y^XZ</span>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-ZUMFDSOUgUw/VZ3vvXq3QuI/AAAAAAAAA7c/1Pzqz6Ilvpg/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B9.38.30%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="320" src="http://3.bp.blogspot.com/-ZUMFDSOUgUw/VZ3vvXq3QuI/AAAAAAAAA7c/1Pzqz6Ilvpg/s320/Screen%2BShot%2B2015-07-08%2Bat%2B9.38.30%2BPM.png" width="293" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Label looks good
    </td>
  </tr>
</table>

<div style="clear: both; text-align: center;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  So, when we print from Adobe Reader we get the expected result. &nbsp;But if we print from PDF Architect or directly from our&nbsp;application we don't get anything and the data is missing entirely! &nbsp;This is a strange issue indeed. &nbsp;The Zebra driver shows its supported formats:
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-FAfnrphI3OY/VZ33yVbMaPI/AAAAAAAAA7s/ntMHTBAVvhU/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B10.24.56%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://4.bp.blogspot.com/-FAfnrphI3OY/VZ33yVbMaPI/AAAAAAAAA7s/ntMHTBAVvhU/s320/Screen%2BShot%2B2015-07-08%2Bat%2B10.24.56%2BPM.png" width="277" /></a>
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  RAW or EMF... &nbsp;The Citrix UPD has two modes, EMF (standard "Citrix Universal Print Driver") or XPS ("Citrix XPS Universal Print Driver"). &nbsp;I would assume <a href="http://support.citrix.com/servlet/KbServlet/download/32205-102-696273/Printing%20Planning%20Guide.pdf">EMF to EMF</a> would be the way to go?
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-YvGa8JBDYno/VZ34mXr2O4I/AAAAAAAAA70/GmURYS8XIyE/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B10.28.44%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="156" src="http://1.bp.blogspot.com/-YvGa8JBDYno/VZ34mXr2O4I/AAAAAAAAA70/GmURYS8XIyE/s320/Screen%2BShot%2B2015-07-08%2Bat%2B10.28.44%2BPM.png" width="320" /></a>
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  By utilizing the 'printer mapped' UPD, avoiding the UPS, we can enable 'Print Preview' functionality and look at the EMF file as it's placed in the print queue. &nbsp;Here's is what I see:
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://4.bp.blogspot.com/-iqxWu0PSHUY/VZ37f6XCl5I/AAAAAAAAA8I/2ozw7N6Z7sY/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B10.41.05%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="167" src="http://4.bp.blogspot.com/-iqxWu0PSHUY/VZ37f6XCl5I/AAAAAAAAA8I/2ozw7N6Z7sY/s320/Screen%2BShot%2B2015-07-08%2Bat%2B10.41.05%2BPM.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Adobe Reader on the left, PDF Architect on the right
    </td>
  </tr>
</table>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  Well, there is content being sent to the print queue from both applications. &nbsp;Adobe Reader looked slightly heavier in its lines vs. PDF Architect but both SPL files had content. &nbsp;This is still very confusing why Adobe Reader actually prints but PDF Architect does not. &nbsp;Maybe it's in the way Adobe Reader processes its file? &nbsp;I don't know. &nbsp;Maybe there's a way to modify EMF on Citrix? &nbsp;It turns out, you can.
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  Citrix offers the ability to modify the way it's EMF driver works by reprocessing it. &nbsp;You can enable this feature via Group Policy. &nbsp;
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-Pw2N-kWQA6w/VZ38NII4xcI/AAAAAAAAA8Q/ScTJm9HMvzk/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B10.43.58%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="174" src="http://2.bp.blogspot.com/-Pw2N-kWQA6w/VZ38NII4xcI/AAAAAAAAA8Q/ScTJm9HMvzk/s320/Screen%2BShot%2B2015-07-08%2Bat%2B10.43.58%2BPM.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Reprocess EMF for printer
    </td>
  </tr>
</table>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  Did it make a difference? &nbsp;No. &nbsp;Still a blank page/no content for PDF Architect, Adobe Reader prints out fine. &nbsp;
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  At this point I was at my ropes end and decided to grasp at straws. &nbsp;
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  The Citrix UPS also allows you use the XPS driver instead of the EMF driver. &nbsp;This would force a completely different rendering path, XPS -> XPS to GDI -> EMF -> GDI/DDI Driver -> PDL Print Device. &nbsp;We know it has to go this way as the Zebra driver only does EMF or RAW and the UPD will only output EMF files. &nbsp;To enable XPS printing I changed the Universal Driver Preference to favour XPS.
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-gZvDIuHP8-g/VZ4A29S2M-I/AAAAAAAAA8k/SbmvS4m3fqY/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B11.03.53%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="213" src="http://4.bp.blogspot.com/-gZvDIuHP8-g/VZ4A29S2M-I/AAAAAAAAA8k/SbmvS4m3fqY/s320/Screen%2BShot%2B2015-07-08%2Bat%2B11.03.53%2BPM.png" width="320" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
</div>

&nbsp;I reprinted to XPS from both PDF Architect and Adobe Reader. &nbsp;Again, Adobe Reader showed up darker but again there is content to be printed from both spools.

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://1.bp.blogspot.com/-ZyRT0FdWg7Y/VZ4AMZ2PZUI/AAAAAAAAA8c/R81iAa5Yc6Y/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B11.00.49%2BPM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="144" src="http://1.bp.blogspot.com/-ZyRT0FdWg7Y/VZ4AMZ2PZUI/AAAAAAAAA8c/R81iAa5Yc6Y/s320/Screen%2BShot%2B2015-07-08%2Bat%2B11.00.49%2BPM.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      PDF Architect on the left, Adobe Reader on the right
    </td>
  </tr>
</table>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  And what did the print queue show?
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  PDF Architect:
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
</div>

<div style="-webkit-text-stroke-color: initial; margin: 0px; padding: 0px;">
  <blockquote>
    <p>
      <span style="font-family: Courier New, Courier, monospace;"> CT~~CD,~CC^~CT~</span><span style="font-family: Courier New, Courier, monospace;">^XA</span><span style="font-family: Courier New, Courier, monospace;">^PW446</span><span style="font-family: Courier New, Courier, monospace;">^FO0,0^GFA,23296,23296,00056,:Z64:</span><span style="font-family: Courier New, Courier, monospace;">eJzt2z9oE2EcxvHnLm8uF7nYawgaJYQjFLFOwdHpUkWDiH87OjQY6+JmcXI4Q4QITTlxcE1shg7ipOBmhg4dRRzEKQiCnRx1q2+S/rGhubw5qgR8PkO45O7L+7vkcpkCEBERERERERERERGpceBZ0GC6ltwW6l2+tbboNm/W3RWx3H6PC0XFLtVae51b/VjvVF6ecZo3ps1QU6uxg3fvnK3mTWqXcL6jChG1nzoKnV7KVNu9LWu2DC+Z0pKbKuvpm61WO8ycP+5+2+7mgY05IRS7PSnZXa7ihO2P142aMx3Y9fcWSoNd8nzgVSyudB+f33k82B1T6LTPuUM8v8Av6qR1esgu55r24C6VznGfhVpv17zvhum0T1+6nbhujrle38UXtRHd9NHqAd3oOQe7uFxoBgn5NdDdhtFQnjOegfEWiWVkI8UFYz2wEy4wZNrgrukb6903MDNeF8MHHaflk7I8sOaZ/Yuu4uPcgkC096Qw6vyOdKBtX6w+zjpV+buo1I0z59/o8ril0mmdKWTRmfJ+aZ0sprBk44HSeieL8iUds6KCgo+NuuqcWbNhIw7NmEOhDW/FU+zSZu/WqMk15bHezmU0Kfezf9lFneO9zdiqfNAzr2rJU3vd1UI8PaS7dv9S/5h7QOTrI2vr9s/do8TDJ6V3Q7pF3d03l7V/zvyQOWNbKg5YDwEm6XNgx44dERERERERERERERERERERERER/Wdsa4y/Fv0hMhOuM96UQ3XwwmVEREREREREdFh+A05IoVY=:EC01</span><span style="font-family: Courier New, Courier, monospace;">^PQ1,0,1,Y^XZ</span>
    </p>
  </blockquote>
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
    Adobe Reader:
  </div>
  
  <div>
  </div>
  
  <div>
    <blockquote>
      <p>
        <span style="font-family: Courier New, Courier, monospace;"> CT~~CD,~CC^~CT~</span><span style="font-family: Courier New, Courier, monospace;">^XA</span><span style="font-family: Courier New, Courier, monospace;">^PW446</span><span style="font-family: Courier New, Courier, monospace;">^FO0,0^GFA,23296,23296,00056,:Z64:</span><span style="font-family: Courier New, Courier, monospace;">eJztmkloE1EYx99MJnHE1CSCVgSbBEVxQy81WtBqpZViEREUitYISsGDeBAsepnXGhcUN+xBEDXFS/FgsaKHSiEIIiiYVBEqGlLXlmJd2yRVk/FNlppM0mQSRS38f8yWN/PL9711cgghAAAAAAAAAAAAAAAAACYo4mLLuguigSyr2zDkfa8L9oc1iiV+6eTQa2fpvd59O/aGmzqWafQa/NL5jpFA6dtnQ4/2hIP9Wr0Y5kIeJqRSVhFVF8ShKk+Vky15YWC7ENsIxxxOHe9fe2LSK3lyXV8hVcuPF60tKJ7R6dVPkd7u/y5ribfk8aC/mDznNjxIesNuYbRCX3FwpZ1oyPM4oQnvsudI2039yY2mGwXVr7A8NXvlh7pyefrNzGmOPs3wHN8GEldimjc14fmYd8x5IMOrDKQN30jsGPpVwG5HWGEks368Kk8+lmR6/dZkaRdeVZLpkeweZSfWbZSmxWOfBMo8yuLRbF6M7tqtp4rJ82Nt6Mfv1K8gT0zxuu7cZZIoxjxRc7w5Dz8q3ui2mrzxdKn93hfPU77YmMcrb9qlDBCnFJJkaczLXz+H9Eo5UWVjB1upr8XafXTWYM1z72eXqdUV9zy56qes2DbrJr70h2Hq9sZaKeQqCedsF6HdbWp3EzMXLqwfJq3vm3S/j3mfiO3D8ipH9TzdtRpuhk9wLHQRE+GO7qxqU3srlO8XGYTMZB1pk6P80qjB5DVwUY9x1VcjmUK5M+41L9VeZXKWBeRfM1GZcXKEfZaUCSkpRdm9/ORrT1W76Ov4FUqbZO8HGvfYDEzxKBUodW1vGWhmPduc6Y2hxLNHZndW9gStPlkXse629shk3PUzLU97ydkv9voyc+8Zfvq5N5Z6E7utyRPNLXZuhpme5s0WwcIZc3piijfzxAKVN1lLPJ+VX829s5Kgrq9MKOOGNeZpyxif43q6kDM+JGLjRWLTMCJLTZLUFFu5lTtsD2XxNI0XmpnnlpHvHuVyGlsJ53f6b8ndl8ae0kdut74gmuqXhpA451uX1CTfTLneR2KOeJP/XJ5/3aNJj/7fecKb0N4YBXnFvo+siZ+cCQJaPQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEw1P4m/IhRK4erYo78rh4uIZirIAAAAAAAAAAAAwcfkJ2Wk8DQ==:9DEC</span><span style="font-family: Courier New, Courier, monospace;">^PQ1,0,1,Y^XZ</span>
      </p>
    </blockquote>
  </div>
  
  <div>
  </div>
  
  <div>
    Success! &nbsp;It appears changing the rendering path to XPS works in resolving the data not getting rendered. &nbsp;When printed from the application with the EMF driver and a blank page was spit out, I tried again with the XPS driver and it worked successfully:
  </div>
  
  <div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a href="http://3.bp.blogspot.com/-_Pgm6VECIHE/VZ4C7feB6HI/AAAAAAAAA8w/LufBLkEAtgQ/s1600/Screen%2BShot%2B2015-07-08%2Bat%2B11.12.35%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="276" src="http://3.bp.blogspot.com/-_Pgm6VECIHE/VZ4C7feB6HI/AAAAAAAAA8w/LufBLkEAtgQ/s320/Screen%2BShot%2B2015-07-08%2Bat%2B11.12.35%2BPM.png" width="320" /></a>
  </div>
  
  <div>
  </div>
  
  <div>
    <blockquote>
      <p>
        <span style="font-family: Courier New, Courier, monospace;">CT~~CD,~CC^~CT~</span><span style="font-family: Courier New, Courier, monospace;">^XA</span><span style="font-family: Courier New, Courier, monospace;">^PW446</span><span style="font-family: Courier New, Courier, monospace;">^FO0,0^GFA,23296,23296,00056,:Z64:</span><span style="font-family: Courier New, Courier, monospace;">eJzt1n1ME2ccwPHnaeFg7hbKusnVmOGcmjkmNsY/DM0sJg7nZpZFo0usstZMNhFlL5lxCt6VZIU5E7YFR30DMqO4SRAZqQsRfFiJss3Ejf6hMAqaxS3dnG0Gdmppb3el2BJKez224bbfJ+FycPflueee9lqEAAAAAAAiJBGc+1yuCnHoDdy5cXZqxouodbkqm4vXUdddK0s+aPlo+idzmvZ/szz304qTVRXmZ+OOpxhYubFw6QHPZv/CurZpa81Pnfj4kZOKtSRel9rz5updv78y1z93/pJjZQWM9usnki5qTXGvMyRJ4nmTkcnLglRR/hfmIn8j4oYRfujgr8Et/i91Bad7mjQHLwSK+gLXt92W3r31falW8+2K7U/3e+v5E7G6Aw35WXdHY7LpdI9Hs1cYr4ot8F6N1R1u2aHLIffmlyTOTyVhftaGDRHjiRuJ9wVHzg8v+hIxN3VX6Aubti1uln5fsMOKNDdndNE/Hm3LaElg/VY3IU2XzkrbjQ2PNspZ99Tw/B72z/tOatdrFLeKge4bGKmdi65I7QyzxC3lHBwWutJ5H0rtulGwq3a8jmW/byfxfqeiXic1uhNxP9HIlhlZh39XlzyYZzDoZHTu1/ZseTfxjrLlGX545/6/L5FdWeuZSxZxp6Z6fksCneWXcy+MdIPPlCTSfd57KU/cOWKbaDx1367yca9aCfObcWh3yvgu2Ykope9XXPZFjrHzp8btOQzlqLvXzRTHO7w+8jpHnh5cipOjlJ+5cJmvcNZ5r7+4kKH662LNL9Ql70eUwufCXPNOY+fm9sKdDHWGi9KpjxWh7G6b+Fc8nMD6Ta8PoKVr+GC3IYEu470htOCru6PjmdsbvVRavq3DXHqKI8p1NYjJer5GKLYKZ9BdjHpZ9PmZiwIHqbS3A8vM7laOKPp6EbPQsFc4tkA448GLjPraBN25Ng81rX8Im9uyEVFerkJM9nqrcCxLOOOhCkbZMdXPwdD6Rb+f3YTujfjclNzlE9oQsysfsv9h0TsyBypd5W7CHtefZfhVe4bX1QkfEbG6Su+KgIXt0ftqb1T6Cd/MDmn4V/m+4OFY3b7F5zMtJod2wLpln57Yj5vOaspX2Yfjd1qKoUyctsNqpLXEjkycxoLs9H3XVXpSblMsp++ovZrmITxiOU0A8X9fN8G6/0Xd+OeZhNc1I7MTr/PlYt1WErrOy03i98jZTyrefwBztjFfbWiCU61zXgrPjy3Z8Vtu6JhTeMjg6lv6n10u1lfvGdPV8jVu3hPuiot3G0bHqxYehvjQrVMFd+6wg+1ju4CzP8sf7v7x+xJ1/cajw7sx1h066KCDboq6TBkPQX4S37MAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA+P96DKkel9Op+fRrcrqZ7nSTnC7djYmcDi2RVQEAAAAAADBF/gTXZRgw:7C79</span><span style="font-family: Courier New, Courier, monospace;">^PQ1,0,1,Y^XZ</span>
      </p>
    </blockquote>
  </div>
  
  <div>
  </div>
  
  <div>
    To that end, I am concluding my work. &nbsp;Unfortunately, I do not know why Adobe Reader always worked without issue where two other applications did not. &nbsp;When on EMF mode, I could send a test page to the printer via print properties and it would have the full content, I could also print from notepad to the EMF queue and it worked just fine. &nbsp;For some reason, whatever path PDF Architect and my other application uses to print to the EMF driver just didn't work. &nbsp;The Citrix EMF Viewer would display content, implying there is data. &nbsp;I do know there are several versions of EMF (NT EMF 1.003-8) you can choose on the printer processor properties, but it appears the Zebra driver ignores those settings in favour of it's selection I posted previously. &nbsp;Forcing a different print processor (HP or Lexmark) in addition to an incompatible print processor type (TEXT/XPS) the Zebra driver will still work. &nbsp;So maybe it's a <a href="https://msdn.microsoft.com/en-us/library/cc231034.aspx">EMF version compatibility</a> thing and whatever version Adobe Reader is passing works? &nbsp;
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->