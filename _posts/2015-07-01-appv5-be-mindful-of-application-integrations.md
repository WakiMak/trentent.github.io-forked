---
id: 563
title: 'AppV5 &#8211; Be mindful of application integrations'
date: 2015-07-01T00:52:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/01/appv5-be-mindful-of-application-integrations/
permalink: /2015/07/01/appv5-be-mindful-of-application-integrations/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/appv5-be-mindful-of-application.html
blogger_internal:
  - /feeds/7977773/posts/default/1489517547269881584
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
When you sequence an application in AppV5 there are special keys that will be &#8216;integrated natively&#8217; into your system. &nbsp;These keys are HKLMSoftwareClasses. &nbsp;There are numerous things that can be integrated and these include things like File Associations or, even worse, libraries. &nbsp;I believe a &#8216;best practice&#8217; should be to make your sequencer and target platform as close as possible by installing the same software on both if you can. &nbsp;For example, for all the Visual C++ native runtime libraries on your target device have that same software on your sequencer too. &nbsp;If they differ than the natively installed package will be usurped by a AppV5 package&#8217;s deployment.

<div>
</div>

<table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
  <tr>
    <td style="text-align: center;">
      <a href="http://2.bp.blogspot.com/-J_zPk2bM_q8/VZOLBpzmLqI/AAAAAAAAAzw/Ylx13IINt-E/s1600/2015-07-01%2B00_38_22.gif" style="margin-left: auto; margin-right: auto;"><img border="0" height="199" src="http://2.bp.blogspot.com/-J_zPk2bM_q8/VZOLBpzmLqI/AAAAAAAAAzw/Ylx13IINt-E/s320/2015-07-01%2B00_38_22.gif" width="320" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      AppV5 Package has usurped a natively installed control
    </td>
  </tr>
</table>

<div>
  </p> 
  
  <div>
    <table align="center" cellpadding="0" cellspacing="0" style="margin-left: auto; margin-right: auto; text-align: center;">
      <tr>
        <td style="text-align: center;">
          <a href="http://3.bp.blogspot.com/-BaDD5GuK2fI/VZOLNZ-fbOI/AAAAAAAAAz4/pY9Qr6oVC0A/s1600/Screen%2BShot%2B2015-07-01%2Bat%2B12.18.16%2BAM.png" style="margin-left: auto; margin-right: auto;"><img border="0" height="196" src="http://3.bp.blogspot.com/-BaDD5GuK2fI/VZOLNZ-fbOI/AAAAAAAAAz4/pY9Qr6oVC0A/s320/Screen%2BShot%2B2015-07-01%2Bat%2B12.18.16%2BAM.png" width="320" /></a>
        </td>
      </tr>
      
      <tr>
        <td style="text-align: center;">
          The differences in the two files
        </td>
      </tr>
    </table>
    
    <div>
    </div>
    
    <div>
    </div>
  </div>
</div>

<div>
  This can cause issues if you are not careful about the deployments of your AppV packages as well as Windows will now natively use some libraries that may have security concerns. &nbsp;This can also cause some issues if poorly written applications have specific version requirements and if multiple packages are installed with overlapping files/keys, it appears the last loaded package wins.
</div>

<div>
</div>

<div>
  In one particular example I made an &#8216;Internet Explorer&#8217; package that changed the registry keys within IE so it would identify itself as IE 6. &nbsp;Doing this resulted in the http key being changed as well:
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-EmkOnjMpkP0/VZOMdlDpDsI/AAAAAAAAA0E/3iuaztqMymA/s1600/Screen%2BShot%2B2015-07-01%2Bat%2B12.44.32%2BAM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="84" src="http://2.bp.blogspot.com/-EmkOnjMpkP0/VZOMdlDpDsI/AAAAAAAAA0E/3iuaztqMymA/s320/Screen%2BShot%2B2015-07-01%2Bat%2B12.44.32%2BAM.png" width="320" /></a>
</div>

<div>
</div>

<div>
  This resulted in every programatic launch of a URL to launch iexplore.exe within a different AppV bubble. &nbsp;This was unexpected as I do Internet Explorer customizations within my packages so that when you launch IE you have the IE customizations for that app all with you. &nbsp;This one package caused issues as http launches to went into this package as opposed to the package you were in when you launched it.
</div>

<div>
</div>

<div>
  So, if you have a machine that loads multiple packages, be aware of these potential issues.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->