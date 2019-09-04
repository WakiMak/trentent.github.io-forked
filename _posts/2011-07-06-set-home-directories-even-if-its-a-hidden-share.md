---
id: 705
title: 'Set home directories even if it&#8217;s a hidden share'
date: 2011-07-06T11:40:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/07/06/set-home-directories-even-if-its-a-hidden-share/
permalink: /2011/07/06/set-home-directories-even-if-its-a-hidden-share/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/07/set-home-directories-even-if-its-hidden.html
blogger_internal:
  - /feeds/7977773/posts/default/7130478756841842650
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - scripting
---
There exists an issue with DSMOD that prevents you from modifying the -hmdir with a share that has a dollar sign in it. According to the dsmod.exe example:

> The special token $username$ (case insensitive) may be used to place the  
> SAM account name in the value of -webpg, -profile, -hmdir, and  
> -email parameter.  
> For example, if the target user DN is  
> CN=Jane Doe,CN=users,CN=microsoft,CN=com and the SAM account name  
> attribute is &#8220;janed,&#8221; the -hmdir parameter can have the following  
> substitution:
> 
> -hmdir users$username$home
> 
> The value of the -hmdir parameter is modified to the following value:
> 
> &#8211; hmdir usersjanedhome

This does not work if your home directory is structured like so:

-hmdir users$$username$home

The value returned by DSMOD is actually:

&#8211; hmdir users$$username$home as opposed to the proper  
users$janedhome

To fix this you can use the awesome ADFIND and ADMOD from Joeware.

The command to fix set it correctly would be:

> <pre class="lang:batch decode:true ">adfind -b "OU=TEST - Trentent,DC=lab,DC=com" -adcsv -f "(&objectClass=user)" samAccountName | admod homeDirectory::\\test\test$\{{samAccountName}} homeDrive::Z:</pre>

Go Joe!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->