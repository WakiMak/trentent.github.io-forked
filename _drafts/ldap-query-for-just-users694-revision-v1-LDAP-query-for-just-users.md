---
id: 1185
title: 'LDAP query for *just* users'
date: 2016-04-25T11:27:31-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/694-revision-v1/
permalink: /2016/04/25/694-revision-v1/
---
We have numerous &#8220;mailbox only&#8221; user accounts in our AD. I&#8217;ve been asked for a query of all the user accounts on our domain. The query needs to exclude these accounts and disabled accounts as we&#8217;re only interested in active user accounts. This is what I came up with:

<pre class="lang:batch decode:true ">adfind -f "&(objectcategory=person)(samaccountname=*)(!(userAccountControl:1.2.840.113556.1.4.803:=2)(!(msExchRecipientTypeDetails=4)(!(msExchRecipientDisplayType=7)(!(msExchRecipientDisplayType=8)(!(extensionattribute1=Service Account))))))" -csv -csvdelim ;</pre>

This query does the following:  
Find all user accounts (objectcategory=person)(samaccountname=*)  
But NOT  
Disabled accounts (userAccountControl:1.2.840.113556.1.4.803:=2)  
Exchange Shared Mailboxes: (msExchRecipientTypeDetails=4)  
Exchange Rooms: (msExchRecipientDisplayType=7)  
Exchange Equipment: (msExchRecipientDisplayType=8)  
Service Accounts: (extensionattribute1=Service Account)

MS Software usually adds &#8220;SERVICE ACCOUNT&#8221; to the extensionattribute1.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->