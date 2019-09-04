---
id: 670
title: Loopback iSCSI targets (aka, Target Error on iSCSI Initiator)
date: 2012-11-11T23:38:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/11/11/loopback-iscsi-targets-aka-target-error-on-iscsi-initiator/
permalink: /2012/11/11/loopback-iscsi-targets-aka-target-error-on-iscsi-initiator/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/11/loopback-iscsi-targets-aka-target-error.html
blogger_internal:
  - /feeds/7977773/posts/default/6965345160352284712
categories:
  - Blog
  - Uncategorized
tags:
  - iSCSI
---
Enable the following registry key:

<http://blogs.technet.com/b/csstwplatform/archive/2012/02/09/windows-svr-2008-r2-iscsi-initiator-getting-quot-target-error-quot-message-when-attempting-to-connect-to-iscsi-target-on-localhost.aspx>

<div style="background-color: white; color: #333333; font-family: 'Segoe UI', 'Lucida Grande', Verdana, Arial, Helvetica, sans-serif; font-size: 12px; line-height: 18.149999618530273px; list-style-type: disc; margin: 0cm 0cm 0pt;">
  <pre class="lang:reg decode:true ">HKLM\Software\Microsoft\iSCSI Target
Value Name: AllowLoopBack
Type: REG_DWORD
Value: 1 (Default is 0)

This will enable you to connect to your iSCSI target.</pre>
  
  <p>
    &nbsp;
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->