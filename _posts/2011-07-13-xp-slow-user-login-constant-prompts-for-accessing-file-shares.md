---
id: 702
title: 'XP &#8211; Slow user login, constant prompts for accessing file shares'
date: 2011-07-13T13:29:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/07/13/xp-slow-user-login-constant-prompts-for-accessing-file-shares/
permalink: /2011/07/13/xp-slow-user-login-constant-prompts-for-accessing-file-shares/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/07/xp-slow-user-login-constant-prompts-for.html
blogger_internal:
  - /feeds/7977773/posts/default/4613930268617426822
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
---
At work we were having an issue that seemed to happen a lot at remote sites. Either login times were glacially slow, users could not access file shares without being prompted over and over again for their credentials and numerous logs of:  
Event Type: Warning  
Event Source: LSASRV  
Event Category: SPNEGO (Negotiator)  
Event ID: 40960  
Date: date  
Time: time  
User: N/A  
Computer: Computername  
Description: The Security System detected an authentication error for the server ldap/dca.acc.local. The failure code from authentication protocol Kerberos was &#8220;There are currently no logon servers available to service the logon request. (0xc000005e)&#8221;.  
For more information, see Help and Support Center at http://support.microsoft.com.  
Data: 0000: c000005e 

Event Type: Warning  
Event Source: LSASRV  
Event Category: SPNEGO (Negotiator)  
Event ID: 40961  
Date: date  
Time: time  
User: N/A  
Computer: Computername  
Description: The Security System could not establish a secured connection with the server ldap/Computername.domain.com. No authentication protocol was available.  
For more information, see Help and Support Center at http://support.microsoft.com.  
Data: 0000: c0000388 

The fix to these issues is to switch Kerberos to UDP. After doing so the warnings disappeared and accessing file shares worked without constant reprompting. As well, logins for these remote users became much, much faster.

The change to set Kerberos to UDP is here:  
http://support.microsoft.com/kb/244474

> Start Registry Editor.  
> Locate and then click the following registry subkey:  
> HKEY\_LOCAL\_MACHINESystemCurrentControlSetControlLsa KerberosParameters  
> Note If the Parameters key does not exist, create it now.  
> On the Edit menu, point to New, and then click DWORD Value.  
> Type MaxPacketSize, and then press ENTER.  
> Double-click MaxPacketSize, type 1 in the Value data box, click to select the Decimal option, and then click OK.  
> Quit Registry Editor.  
> Restart your computer.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->