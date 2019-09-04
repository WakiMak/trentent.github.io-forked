---
id: 710
title: Configure Local Group Policy via the command line
date: 2011-04-29T09:19:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/04/29/configure-local-group-policy-via-the-command-line/
permalink: /2011/04/29/configure-local-group-policy-via-the-command-line/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/04/configure-local-group-policy-via.html
blogger_internal:
  - /feeds/7977773/posts/default/29541153333570986
categories:
  - Blog
  - Uncategorized
tags:
  - Group Policy
  - scripting
---
Apply a security policy using an .inf file

[Unicode]Unicode=yes  
[Version]signature=&#8221;$CHICAGO$&#8221;  
Revision=1  
[Profile Description]  
Description=profile description  
[System Access]  
MinimumPasswordAge = 10  
MaximumPasswordAge = 30  
MinimumPasswordLength = 6  
RequireLogonToChangePassword = 0  
NewAdministratorName = &#8220;NewAdminAccountName&#8221;  
NewGuestName = &#8220;NewGuestAccountName&#8221;

Simple to apply from a cmd line

<pre class="lang:default decode:true ">secedit /configure /db %windir%\security\database\localdb.sdb /cfg %systemdrive%\install\local\policy\policyname.inf /verbose</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->