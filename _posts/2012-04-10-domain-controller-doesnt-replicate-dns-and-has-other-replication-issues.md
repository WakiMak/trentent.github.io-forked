---
id: 679
title: 'Domain Controller doesn&#8217;t replicate DNS and has other replication issues'
date: 2012-04-10T13:42:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/04/10/domain-controller-doesnt-replicate-dns-and-has-other-replication-issues/
permalink: /2012/04/10/domain-controller-doesnt-replicate-dns-and-has-other-replication-issues/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/04/domain-controller-doesnt-replicate-dns.html
blogger_internal:
  - /feeds/7977773/posts/default/7465645410088406569
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - DNS
---
We recently demoted a global catalog domain controller and then re-promoted because of issues we were having post-demotion. When a DC is demoted it changes it&#8217;s computer account to have less rights then it would if it were a DC. Somewhere along the line the promotion didn&#8217;t change it&#8217;s account back and after the computer account password expired we started having replications issues. This didn&#8217;t really affect us too much until 14 days after the password expired and the DC couldn&#8217;t replicate back to the domain. All of our DNS zones couldn&#8217;t replicate to it and subsequently became &#8220;stale&#8221; and were scavenged and removed. This caused issues for everyone at that site as they couldn&#8217;t access various resources that we utilize DNS for.

The symptoms were:  
All DNS zones were gone except for the primary zone.  
&#8220;error no trust sam account&#8221; occurred while running &#8220;nltest /dsregdns&#8221;  
This error was in the DNS event log:

> &#8220;The DNS server detected that it is not enlisted in the replication scope of the directory partition ForestDnsZones.ccs.corp. This prevents the zones that should be replicated to all DNS servers in the ccs.corp forest from replicating to this DNS server.
> 
> To create or repair the forest-wide DNS directory partition, open the the DNS console. Right-click the applicable DNS server, and then click &#8216;Create Default Application Directory Partitions&#8217;. Follow the instructions to create the default DNS application directory partitions. For more information, see &#8216;To create the default DNS application directory partitions&#8217; in Help and Support. &#8220;

And this error:

> The attempt to establish a replication link for the following writable directory partition failed.

dcdiag reported the last replication was 2 weeks ago  
repadmin /showreps reported it failed.

The solution was from here:  
http://support.microsoft.com/default.aspx?scid=kb;en-us;329860

> WARNING: If you use the ADSI Edit snap-in, the LDP utility, or any other LDAP version 3 client, and you incorrectly modify the attributes of Active Directory objects, you can cause serious problems. These problems may require you to reinstall Microsoft Windows 2000 Server, Microsoft Exchange 2000 Server, or both. Microsoft cannot guarantee that problems that occur if you incorrectly modify Active Directory object attributes can be solved. Modify these attributes at your own risk.  
> On a domain controller that is in the &#8220;healthy&#8221; part of the domain (not the domain controller with which you experience the issue), install the Windows 2000 Support Tools if they have not already been installed. For additional information about how to install the Windows 2000 Support Tools, click the article number below to view the article in the Microsoft Knowledge Base:  
> 301423 How to Install the Windows 2000 Support Tools to a Windows 2000 Server-Based Computer  
> Start the ADSI Edit snap-in. To do so, click Start, point to Programs, point to Windows 2000 Support Tools, point to Tools, and then click ADSI Edit.  
> Expand Domain NC \[server.example.com\] (where server is the name of the domain controller and example.com is the name of the domain.  
> Expand DC=example,DC=com.  
> Expand OU=Domain Controllers, right-click CN=ServerName (where ServerName is the domain controller with which you experience the issues that are described in the &#8220;Symptoms&#8221; section of this article), and then click Properties.  
> Click the Attributes tab (if it is not already selected).  
> In the Select which properties to view list, click Both, and then click userAccountControl in the Select a property to view list.  
> If the Value(s) box does not contain 532480, type 532480 in the Edit Attribute box, and then click Set.  
> Click Apply, click OK, and then quit the ADSI Edit snap-in

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->