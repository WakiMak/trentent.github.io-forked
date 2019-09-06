---
id: 549
title: Performance differences in Citrix HDX Thinwire Encoders
date: 2015-09-04T14:51:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/04/performance-differences-in-citrix-hdx-thinwire-encoders/
permalink: /2015/09/04/performance-differences-in-citrix-hdx-thinwire-encoders/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/performance-differences-in-citrix-hdx.html
blogger_internal:
  - /feeds/7977773/posts/default/89860611722925948
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - HDX
  - XenDesktop
---
Per my previous post, changing the [Citrix HDX Thinwire Encoder on the fly](http://trentent.blogspot.com/2015/09/change-citrix-hdx-encoder-on-fly-for.html), we can test the performance differences in the different encoder's Citrix provides. &nbsp;I have done so by running through a demo of the [Uniengine Heaven](https://unigine.com/en/products/benchmarks/heaven/) benchmark. &nbsp;The demo is exactly 4 minutes and 20 seconds long. &nbsp;I did a perfmon trace of the CPU %, total bytes sent in MBits/sec and the Thinwire Output in MBit/sec.

Time for some results!

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s1600/CompatibilityMode.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="216" src="http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s320/CompatibilityMode.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Compatibility Mode (Encoder 0x0)
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://3.bp.blogspot.com/-0SJN6ODj1S8/VeoDUwGtFRI/AAAAAAAABFs/DO35eC5pEUA/s1600/DeepCompressionV2.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="216" src="http://3.bp.blogspot.com/-0SJN6ODj1S8/VeoDUwGtFRI/AAAAAAAABFs/DO35eC5pEUA/s320/DeepCompressionV2.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      DeepCompressionV2Encoder (Encoder 0x1)
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-SyWdFOdflWw/VeoHc1xA7EI/AAAAAAAABF0/7dSZVWPwgW0/s1600/DeepCompressionEncoderA.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="216" src="http://2.bp.blogspot.com/-SyWdFOdflWw/VeoHc1xA7EI/AAAAAAAABF0/7dSZVWPwgW0/s320/DeepCompressionEncoderA.png" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      DeepCompressionEncoder (Encoder 0x2)
    </td>
  </tr>
</table>

<div style="clear: both; text-align: center;">
</div>

<div style="clear: both; text-align: center;">
  (Rollover the mouse on the next images to compare graphs)
</div>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <img onmouseout="this.src='http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s640/CompatibilityMode.png'" onmouseover="this.src='http://3.bp.blogspot.com/-0SJN6ODj1S8/VeoDUwGtFRI/AAAAAAAABFs/DO35eC5pEUA/s640/DeepCompressionV2.png'" src="http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s640/CompatibilityMode.png" style="margin-left: auto; margin-right: auto;" />
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      CompatibilityMode vs DeepCompressionV2Encoder</p>
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <img onmouseout="this.src='http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s640/CompatibilityMode.png'" onmouseover="this.src='http://2.bp.blogspot.com/-SyWdFOdflWw/VeoHc1xA7EI/AAAAAAAABF0/7dSZVWPwgW0/s640/DeepCompressionEncoderA.png'" src="http://3.bp.blogspot.com/-79CsBrzEI7g/VeoDVGu8uCI/AAAAAAAABFc/FWnYF18die4/s640/CompatibilityMode.png" style="margin-left: auto; margin-right: auto;" />
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      CompatibilityMode vs DeepCompressionEncoder</p>
    </td>
  </tr>
</table>



<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <img onmouseout="this.src='http://3.bp.blogspot.com/-0SJN6ODj1S8/VeoDUwGtFRI/AAAAAAAABFs/DO35eC5pEUA/s640/DeepCompressionV2.png'" onmouseover="this.src='http://2.bp.blogspot.com/-SyWdFOdflWw/VeoHc1xA7EI/AAAAAAAABF0/7dSZVWPwgW0/s640/DeepCompressionEncoderA.png'" src="http://3.bp.blogspot.com/-0SJN6ODj1S8/VeoDUwGtFRI/AAAAAAAABFs/DO35eC5pEUA/s640/DeepCompressionV2.png" style="margin-left: auto; margin-right: auto;" />
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      DeepCompressionV2Encoder vs DeepCompressionEncoder</p>
    </td>
  </tr>
</table>



<div>
  The cumulative totals should help us get an understanding of the differences between the encoders:
</div>

<div>
</div>

<div>
  <table border="0" cellpadding="0" cellspacing="0" style="border-collapse: collapse; width: 453px;">
    <!--StartFragment--></p> <colgroup> <col style="mso-width-alt: 6400; mso-width-source: userset; width: 150pt;" width="150"></col> <col style="mso-width-alt: 2986; mso-width-source: userset; width: 70pt;" width="70"></col> <col style="mso-width-alt: 4010; mso-width-source: userset; width: 94pt;" width="94"></col> <col style="mso-width-alt: 5930; mso-width-source: userset; width: 139pt;" width="139"></col> </colgroup> 
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="background-color: #4f81bd; border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; height: 15pt; text-align: right; text-underline-style: none; width: 150pt;" width="150">
        &nbsp;&nbsp;
      </td>
      
      <td style="background-color: #4f81bd; border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none; width: 70pt;" width="70">
        CPU Total
      </td>
      
      <td style="background-color: #4f81bd; border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none; width: 94pt;" width="94">
        ThinWire Total
      </td>
      
      <td style="background-color: #4f81bd; border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none; width: 139pt;" width="139">
        Network Total (Mbytes)
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        DeepCompressionEncoder
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        5531.00
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        3693.28
      </td>
      
      <td align="right" style="border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        540.51
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        DeepCompressionV2Encoder
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        5621.67
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        3684.75
      </td>
      
      <td align="right" style="border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        539.74
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none solid solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        CompatibilityMode
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-style: solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        4197.54
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-style: solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        3690.58
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        553.21
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="height: 15.0pt;">
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="height: 15.0pt;">
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="background-color: #4f81bd; border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; height: 15pt; text-align: right; text-underline-style: none;">
        &nbsp;&nbsp;
      </td>
      
      <td style="background-color: #4f81bd; border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none;">
        CPU Total
      </td>
      
      <td style="background-color: #4f81bd; border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none;">
        ThinWire Total
      </td>
      
      <td style="background-color: #4f81bd; border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; color: white; font-family: Calibri; font-size: 12pt; font-weight: 700; text-align: right; text-underline-style: none;">
        Network Total (Mbytes)
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        DeepCompressionEncoder
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        98.4%
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        100.0%
      </td>
      
      <td align="right" style="border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        97.7%
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none none solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        DeepCompressionV2Encoder
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        100.0%
      </td>
      
      <td align="right" style="border-style: solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        99.8%
      </td>
      
      <td align="right" style="border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid none none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        97.6%
      </td>
    </tr>
    
    <tr height="15" style="height: 15.0pt;">
      <td height="15" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-left-color: rgb(79, 129, 189); border-left-width: 0.5pt; border-style: solid none solid solid; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; height: 15pt; text-underline-style: none;">
        CompatibilityMode
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-style: solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        74.7%
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-style: solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        99.9%
      </td>
      
      <td align="right" style="border-bottom-color: rgb(79, 129, 189); border-bottom-width: 0.5pt; border-right-color: rgb(79, 129, 189); border-right-width: 0.5pt; border-style: solid solid solid none; border-top-color: rgb(79, 129, 189); border-top-width: 0.5pt; font-family: Calibri; font-size: 12pt; text-underline-style: none;">
        100.0%
      </td>
    </tr>
    
    <p>
      <!--EndFragment--></tbody> </table> </div> 
      
      <div>
        Interestingly, CompatibilityMode uses 25% less CPU then either DeepCompression Encoder. &nbsp;From what I see though the frames per second appears less for CompatibilityMode then the other two.
      </div>
      
      <!-- AddThis Advanced Settings generic via filter on the_content -->
      
      <!-- AddThis Share Buttons generic via filter on the_content -->