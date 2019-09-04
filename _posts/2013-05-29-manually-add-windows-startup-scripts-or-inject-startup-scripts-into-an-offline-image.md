---
id: 641
title: Manually add Windows startup scripts (or inject startup scripts into an offline image)
date: 2013-05-29T11:38:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/29/manually-add-windows-startup-scripts-or-inject-startup-scripts-into-an-offline-image/
permalink: /2013/05/29/manually-add-windows-startup-scripts-or-inject-startup-scripts-into-an-offline-image/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/manually-add-windows-startup-scripts-or.html
blogger_internal:
  - /feeds/7977773/posts/default/3149693847613700737
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Group Policy
  - Provisioning Services
---
Due to the CGP issue, our solution is to add a startup script to each vDisk. Â Since I don&#8217;t want to make a version of each vDisk than attach it to a server than boot it up than gpedit.msc&#8230; Â We have around 10 vDisks and that process would be annoying and take a while. Â So I decided to investigate doing it offline as mounting a VHD using cvhdmount.exe and then injecting the startup script would be a lot easier.

<div style="clear: both; text-align: center;">
</div>

<div>
  <div>
    To do that, one simply needs to browse to:
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://4.bp.blogspot.com/-d-D9FbtjNvE/UaY8Z5MgwzI/AAAAAAAAASw/AdSoMtivoZQ/s1600/3.PNG"><img src="http://4.bp.blogspot.com/-d-D9FbtjNvE/UaY8Z5MgwzI/AAAAAAAAASw/AdSoMtivoZQ/s320/3.PNG" width="320" height="164" border="0" /></a>
  </div>
  
  <div>
    <div>
      &#8220;C:\windows\system32\GroupPolicy\Machine\Scripts\Startup&#8221; (for machine startup script, aka, a script that starts when your computer starts up) or &#8220;C:\windows\system32\GroupPolicyUsers\Machine\Scripts\Startup&#8221; (for all users startup script [I&#8217;m assuming since I actually didn&#8217;t go through and test the user portion]) and copy your script file there.
    </div>
    
    <div>
    </div>
    
    <div>
      Then, back out one level to C:\windows\system32\GroupPolicy\Machine\Scripts and edit Scripts.ini to include your new script file; incrementing the last line.
    </div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://1.bp.blogspot.com/-tFBgBk1u6Yk/UaY8Zg6iqDI/AAAAAAAAASs/q8RZZNLePa8/s1600/1.PNG"><img src="http://1.bp.blogspot.com/-tFBgBk1u6Yk/UaY8Zg6iqDI/AAAAAAAAASs/q8RZZNLePa8/s320/1.PNG" width="320" height="128" border="0" /></a>
  </div>
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div>
  </div>
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://4.bp.blogspot.com/-vuk2OtDCNnY/UaY8ZpWKgAI/AAAAAAAAASo/YWhc5pwjUG0/s1600/2.PNG"><img src="http://4.bp.blogspot.com/-vuk2OtDCNnY/UaY8ZpWKgAI/AAAAAAAAASo/YWhc5pwjUG0/s320/2.PNG" width="320" height="160" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
    To:
  </div>
  
  <div>
  </div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://4.bp.blogspot.com/-OwO1nGenXa8/UaY8aHxu_TI/AAAAAAAAAS4/nSsOYwdYo3k/s1600/4.PNG"><img src="http://4.bp.blogspot.com/-OwO1nGenXa8/UaY8aHxu_TI/AAAAAAAAAS4/nSsOYwdYo3k/s320/4.PNG" width="320" height="202" border="0" /></a>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->