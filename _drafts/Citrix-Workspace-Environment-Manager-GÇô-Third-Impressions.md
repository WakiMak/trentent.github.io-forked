---
id: 2286
title: Citrix Workspace Environment Manager â€“ Third Impressions
date: 2017-05-19T07:39:11-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2286
permalink: /?p=2286
categories:
  - Uncategorized
---
Citrix Workspace Environment hasÂ some other caveats. Â It doesn&#8217;t really do REG\_BINARY even though there are options for it. Â What happens is REG\_BINARY is interpreted in the ASCII field of the

REG\_BINARY instead of the REG\_BINARY portion. Â Here&#8217;s an example of what I&#8217;m talking about:

<img class="aligncenter size-large wp-image-2292" src="http://theorypc.ca/wp-content/uploads/2017/05/image001.png" alt="" width="666" height="645" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image001.png 666w, http://theorypc.ca/wp-content/uploads/2017/05/image001-300x291.png 300w" sizes="(max-width: 666px) 100vw, 666px" /> 

I want to set the UserPreferencesMask key. Â It&#8217;s a REG_BINARY.

<img class="aligncenter size-large wp-image-2291" src="http://theorypc.ca/wp-content/uploads/2017/05/image002.png" alt="" width="430" height="632" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image002.png 430w, http://theorypc.ca/wp-content/uploads/2017/05/image002-204x300.png 204w" sizes="(max-width: 430px) 100vw, 430px" /> 

It seems simple enough. Â Create the key in WEM. Â But when you do&#8230;

<img class="aligncenter size-large wp-image-2290" src="http://theorypc.ca/wp-content/uploads/2017/05/image003.png" alt="" width="362" height="312" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image003.png 362w, http://theorypc.ca/wp-content/uploads/2017/05/image003-300x259.png 300w" sizes="(max-width: 362px) 100vw, 362px" /> 

The value is applied to the ASCII portion of WEM. Â So the actual value data is completely incorrect. Â Can we set the data to beÂ _binary ASCII_?  
<img class="aligncenter size-large wp-image-2289" src="http://theorypc.ca/wp-content/uploads/2017/05/image004.png" alt="" width="655" height="585" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image004.png 655w, http://theorypc.ca/wp-content/uploads/2017/05/image004-300x268.png 300w" sizes="(max-width: 655px) 100vw, 655px" /> 

Using a HEX to ASCII text converter I got a string that represents the data I want to apply. Â I put it into WEM:  
<img class="aligncenter size-large wp-image-2288" src="http://theorypc.ca/wp-content/uploads/2017/05/image005.png" alt="" width="429" height="634" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image005.png 429w, http://theorypc.ca/wp-content/uploads/2017/05/image005-203x300.png 203w" sizes="(max-width: 429px) 100vw, 429px" /> 

&nbsp;

And I get close, but no cigar. Â The data is still wrong:

<img class="aligncenter size-full wp-image-2287" src="http://theorypc.ca/wp-content/uploads/2017/05/image006.png" alt="" width="357" height="22" srcset="http://theorypc.ca/wp-content/uploads/2017/05/image006.png 357w, http://theorypc.ca/wp-content/uploads/2017/05/image006-300x18.png 300w" sizes="(max-width: 357px) 100vw, 357px" /> 

Talking to Citrix there is no way to apply this data correctly using the registry portion of WEM. Â The workaround suggested is to use a startup script in WEM and appl

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->