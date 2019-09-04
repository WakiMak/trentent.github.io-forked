---
id: 694
title: 'LDAP query for *just* users'
date: 2011-10-14T11:15:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/10/14/ldap-query-for-just-users/
permalink: /2011/10/14/ldap-query-for-just-users/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/10/ldap-query-for-just-users.html
blogger_internal:
  - /feeds/7977773/posts/default/5174230113935570334
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - scripting
---
We have numerous "mailbox only" user accounts in our AD. I've been asked for a query of all the user accounts on our domain. The query needs to exclude these accounts and disabled accounts as we're only interested in active user accounts. This is what I came up with:

<pre class="lang:batch decode:true ">adfind -f "&(objectcategory=person)(samaccountname=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)(!(msExchRecipientTypeDetails=4)(!(msExchRecipientDisplayType=7)(!(msExchRecipientDisplayType=8)(!(extensionattribute1=Service Account))))))" -csv -csvdelim ;</pre>

This query does the following:  
Find all user accounts (objectcategory=person)(samaccountname=*)  
But NOT  
Disabled accounts (userAccountControl:1.2.840.113556.1.4.803:=2)  
Exchange Shared Mailboxes: (msExchRecipientTypeDetails=4)  
Exchange Rooms: (msExchRecipientDisplayType=7)  
Exchange Equipment: (msExchRecipientDisplayType=8)  
Service Accounts: (extensionattribute1=Service Account)

MS Software usually adds "SERVICE ACCOUNT" to the extensionattribute1.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->