---
id: 647
title: AppV Error 25109 - The installation program could not create the configuration data store.  Please see the installation log file for more information.
date: 2013-05-09T11:31:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/05/09/appv-error-25109-the-installation-program-could-not-create-the-configuration-data-store-please-see-the-installation-log-file-for-more-information/
permalink: /2013/05/09/appv-error-25109-the-installation-program-could-not-create-the-configuration-data-store-please-see-the-installation-log-file-for-more-information/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/05/appv-error-25109-installation-program.html
blogger_internal:
  - /feeds/7977773/posts/default/737819900900066244
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
We recently encountered this issue with AppV 4.6 Management server when installing an additional AppV streaming server.

<img class="alignnone size-full wp-image-221" title="Untitled" src="http://dipanmpatel.files.wordpress.com/2012/08/untitled.png?w=595" alt="" /> 

The issue appeared to us to be related to a permissions issue on the database. For some awful reason, to resolve this issue we had to add the SYSADMIN right to the account on the SQL database. Since we are running an enterprise solution here, adding the user to the Domain Admins as suggested all over the web would not work as we segregate those services. It also makes sense that adding the account to the Domain Admins would work on the smaller environments because SQL Express uses AD permissions and would add the Domain Admins to the highest privileges to the local install of SQL Express.

So, for enterprise accounts out there, Microsoft has a horrible setup for adding servers to AppV as the account that does the install needs the highest rights on the DB. I don't know why but it really screams to me that this is poor design.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->