---
id: 1583
title: Citrix Client Side Extensions 1.7.6+ breaks policies
date: 2016-07-14T09:36:31-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1583
permalink: /2016/07/14/citrix-client-side-extensions-1-7-6-breaks-policies/
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - Registry

---
We apply our several group policy settings via Active Directory group policy objects, within them we set 'Citrix' policies.  This includes things like 'Licensing', platform, etc.

<img class="aligncenter wp-image-1584 size-large" src="http://theorypc.ca/wp-content/uploads/2016/07/AD_Policies-1024x880.png" alt="AD_Policies" width="1024" height="880" srcset="http://theorypc.ca/wp-content/uploads/2016/07/AD_Policies-1024x880.png 1024w, http://theorypc.ca/wp-content/uploads/2016/07/AD_Policies-300x258.png 300w, http://theorypc.ca/wp-content/uploads/2016/07/AD_Policies-768x660.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

&nbsp;

When we upgraded the Citrix Group Policy Client Side Extensions (x86) to version 1.7.6 or 1.7.7 we found that these values were no longer being applied.  FYI!

&nbsp;  
  
&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->