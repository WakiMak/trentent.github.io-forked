---
id: 532
title: Citrix Director 7.7 is giving me artifacts!
date: 2016-01-19T16:04:00-06:00
author: trententtye
layout: post
permalink: /2016/01/19/citrix-director-7-7-is-giving-me-artifacts/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/01/citrix-director-77-is-giving-me.html
blogger_internal:
  - /feeds/7977773/posts/default/1716000290563093399
image: /wp-content/uploads/2016/01/desktop-director-bad-1.png
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Director
  - Internet Explorer
---
We are still in a pilot-preupgrade phase of Citrix Director 7.7 and found an issue where we were getting artifacts with IE11.  The artifacts manifested themselves like so:

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/desktop-director-bad-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/desktop-director-bad-1-300x125.png" width="320" height="133" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is wrong.
    </td>
  </tr>
</table>

<div>
</div>

<div>
</div>

<div>
  When the search box should look like this:
</div>

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/director-good-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/director-good-1-300x169.png" width="320" height="180" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      This is right.
    </td>
  </tr>
</table>

<div>
</div>

<div>
  This issue appears to be caused by a policy our organization uses to enable compatibility view for sites on the Local Intranet:
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/compatib-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/compatib-1-254x300.png" width="270" height="320" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      The Culprit
    </td>
  </tr>
</table>

<div>
  Citrix says the following about Director and 'Compatibility View':
</div>

[**Director does not support Internet Explorer compatibility mode.** Please use the recommended browser settings. When you install Internet Explorer, accept the default to use the recommended security and compatibility settings. If you already installed the browser and chose not to use the recommended settings, go to Tools > Internet Options > Advanced > Reset and follow the instructions](https://www.citrix.com/blogs/2014/06/09/browser-best-practices-for-citrix-director-4/)

<div>
</div>

<div>
  Because we use a group policy to push this setting out, disabling it and re-enabling it on a site-per-site configuration isn't acceptable.  There is an option to set the 'zone assignment' of our Citrix Director server to be on 'Trusted Sites' instead of 'Local Intranet' but this would be another policy that would have to be pushed out to the ~80,000 workstations we have.  Instead, there is another option.  We can edit the default.html file in the Citrix Director folder and add a line in thesection to tell IE to exclude this site from compatibility mode.  To execute this:
</div>

<div>
</div>

<div>
  Edit the Default.html file ("C:inetpubwwwrootDirectordefault.html") and add this line:
</div>

<div>
  <pre class="lang:default decode:true "><meta http-equiv="X-UA-Compatible" content="IE=9; IE=8; IE=EDGE" /></pre>
</div>

To the 'head' section.  Example:

<div>
</div>

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-19-2Bat-2B3.03.26-2BPM-1.png"><img src="http://theorypc.ca/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-19-2Bat-2B3.03.26-2BPM-1-300x135.png" width="320" height="143" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Add the line around here
    </td>
  </tr>
</table>

<div>
  Delete your cache and now your website will work without issue.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
