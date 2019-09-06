---
id: 704
title: ADFind one-liner -> Find operating system of computer in AD
date: 2011-07-08T09:52:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/07/08/adfind-one-liner-find-operating-system-of-computer-in-ad/
permalink: /2011/07/08/adfind-one-liner-find-operating-system-of-computer-in-ad/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/07/adfind-one-liner-find-operating-system.html
blogger_internal:
  - /feeds/7977773/posts/default/6657458376771427764
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - scripting
---
Nice 🙂

> <pre class="lang:default decode:true  ">adfind -b "OU=Domain Controllers,DC=lab,DC=com" -f "&(objectcategory=computer)" operatingSystem -csv</pre>
> 
> &nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->