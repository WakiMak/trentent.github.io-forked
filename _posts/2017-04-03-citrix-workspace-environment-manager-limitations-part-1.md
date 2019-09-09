---
id: 2088
title: Citrix Workspace Environment Manager - Limitations (part 1?)
date: 2017-04-03T15:46:29-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2088
permalink: /2017/04/03/citrix-workspace-environment-manager-limitations-part-1/
image: /wp-content/uploads/2017/04/WEM_REGISTRY_ICON.png
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - Performance
  - Workspace Environment Manager
---
In a brief evaluation of Citrix Workspace Environment Manager, I looked at the utility of the product to replace Group Policy Preferences (GPP) _in a **&** environment context_.  For this I focused on replacing a set of registry keys we apply via GPP for our & environment.  My results were not favorable for using WEM in this context for the Registry portion as WEM pushes processing of it's entries into the 'shell' session.  For &, the Shell session is typically applied quickly and so the application may launch **without** those keys present (which is bad - the application needs those keys present _first_).  So although logon times maybe reduced, this scenario does not work _for the Registry portion_.  We are still exploring the effects of WEM and whether some other components that operate synchronously within GPP are needed.  Can these components be moved to WEM?  One of the big 'wins' for this approach maybe Drive Mappings, which apply synchronously and requires the Drive Mappings to be processed before allowing a user to logon.  Moving this to WEM may be a win worth exploring...  **IF** the application doesn't require drive mappings before being launched. But that's for another article..

However, for the registry portion of WEM we did encounter a few 'gotcha's worth mentioning if you are going to use WEM.

# WEM does not do 'Registry Binary' keys.

Well...  it says it does.  And it kind of does.  But odds are you are not going to get the results you expect.

Looking at a simple REG_BINARY key it contains data is displayed as 'hexadecimal' data.  
<img class="aligncenter size-full wp-image-2090" src="/wp-content/uploads/2017/04/REG_BINARY-1.png" alt="" width="666" height="645" srcset="/wp-content/uploads/2017/04/REG_BINARY-1.png 666w, /wp-content/uploads/2017/04/REG_BINARY-1-300x291.png 300w" sizes="(max-width: 666px) 100vw, 666px" /> 

If I want to use WEM to apply this key, I would create an entry within WEM:

<div id="attachment_2091" style="width: 440px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2091" class="wp-image-2091 size-full" src="/wp-content/uploads/2017/04/WEM_REG_BINARY.png" alt="HINT: it's not." width="430" height="632" srcset="/wp-content/uploads/2017/04/WEM_REG_BINARY.png 430w, /wp-content/uploads/2017/04/WEM_REG_BINARY-204x300.png 204w" sizes="(max-width: 430px) 100vw, 430px" /></p> 
  
  <p id="caption-attachment-2091" class="wp-caption-text">
    Does this look correct to you? I thought it looked correct to me.
  </p>
</div>

&nbsp;

However, when I apply this key I get a completely wrong result.  Why?  Because WEM is applying the hexadecimal code in the ASCII field of the REG_BINARY

<img class="aligncenter size-full wp-image-2092" src="/wp-content/uploads/2017/04/WEM_REG_BINARY_Applied.png" alt="" width="362" height="312" srcset="/wp-content/uploads/2017/04/WEM_REG_BINARY_Applied.png 362w, /wp-content/uploads/2017/04/WEM_REG_BINARY_Applied-300x259.png 300w" sizes="(max-width: 362px) 100vw, 362px" /> 

In order to get WEM to apply the code properly we would need to copy/convert the ASCII from the REG_BINARY.

<img class="aligncenter size-full wp-image-2093" src="/wp-content/uploads/2017/04/Convert.png" alt="" width="655" height="585" srcset="/wp-content/uploads/2017/04/Convert.png 655w, /wp-content/uploads/2017/04/Convert-300x268.png 300w" sizes="(max-width: 655px) 100vw, 655px" /> 

&nbsp;

However, I have bad results doing so:

<div id="attachment_2094" style="width: 439px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2094" class="wp-image-2094 size-full" src="/wp-content/uploads/2017/04/WEM_With_Binary_ASCII.png" width="429" height="634" srcset="/wp-content/uploads/2017/04/WEM_With_Binary_ASCII.png 429w, /wp-content/uploads/2017/04/WEM_With_Binary_ASCII-203x300.png 203w" sizes="(max-width: 429px) 100vw, 429px" /></p> 
  
  <p id="caption-attachment-2094" class="wp-caption-text">
    WEM with ASCII converted binary values applied.
  </p>
</div>

&nbsp;

Result:

<div id="attachment_2095" style="width: 367px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2095" class="wp-image-2095 size-full" src="/wp-content/uploads/2017/04/REG_BINARY_WITH_ASCII_WEM.png" width="357" height="22" srcset="/wp-content/uploads/2017/04/REG_BINARY_WITH_ASCII_WEM.png 357w, /wp-content/uploads/2017/04/REG_BINARY_WITH_ASCII_WEM-300x18.png 300w" sizes="(max-width: 357px) 100vw, 357px" /></p> 
  
  <p id="caption-attachment-2095" class="wp-caption-text">
    This is closer but grossly wrong.
  </p>
</div>

&nbsp;

Why is this wrong?  WEM stores everything as XML.  And XML files do not like storing binary or non-ascii data.  WEM stores these values as their ASCII representations and not REG\_BINARY representations so if your REG\_BINARY (which, there's a 99% chance) contains a non-ASCII character it will fail to apply the key properly.

<img class="aligncenter size-full wp-image-2096" src="/wp-content/uploads/2017/04/Bad_XML.png" alt="" width="706" height="780" srcset="/wp-content/uploads/2017/04/Bad_XML.png 706w, /wp-content/uploads/2017/04/Bad_XML-272x300.png 272w" sizes="(max-width: 706px) 100vw, 706px" /> 

&nbsp;

Even worse, during my time fiddling with this, I BROKE WEM.

<img class="aligncenter size-full wp-image-2097" src="/wp-content/uploads/2017/04/BAD_XML_BROKE_WEM.png" alt="" width="359" height="129" srcset="/wp-content/uploads/2017/04/BAD_XML_BROKE_WEM.png 359w, /wp-content/uploads/2017/04/BAD_XML_BROKE_WEM-300x108.png 300w" sizes="(max-width: 359px) 100vw, 359px" /> 

If the ASCII representation of "&x1" or whatever was set it caused WEM to crash when applying the values.  So REG\_BINARY's are completely out.  In my limited testing, we only had 1 REG\_BINARY to apply, but in our environment we use GPP to apply 5 different REG\_BINARY keys.  So using WEM for these applications is right out.  I filed and asked Citrix for a 'feature enhancement' to support applying REG\_BINARY's properly, but I was told this was operating as expected so I'm not holding my breathe but this does limit the utility of WEM.

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->