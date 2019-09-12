---
id: 686
title: Registry keys needed to set a default server Exchange server for Outlook
date: 2012-02-16T17:35:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/02/16/registry-keys-needed-to-set-a-default-server-exchange-server-for-outlook/
permalink: /2012/02/16/registry-keys-needed-to-set-a-default-server-exchange-server-for-outlook/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/02/registry-keys-needed-to-set-default.html
blogger_internal:
  - /feeds/7977773/posts/default/2742314949247237502
categories:
  - Blog
  - Uncategorized
tags:
  - Group Policy
  - Registry
---
We recently upgraded our Exchange infrastructure from 2007 to 2010. During this we changed the host name of the server from an internal name to a nice, easy to remember one (outlook.company.corp). But, we did not update our Outlook's defaults to this server, so when you open Outlook for the first time you are presented with this message:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-KJKF0dsFBi4/Tz2QWYEIV1I/AAAAAAAAAIs/WmYYhUXubLw/s1600/exchange-error.png"><img src="http://1.bp.blogspot.com/-KJKF0dsFBi4/Tz2QWYEIV1I/AAAAAAAAAIs/WmYYhUXubLw/s400/exchange-error.png" width="400" height="225" border="0" /></a>
</div>

Microsoft Exchange is unavailable.

With the options:  
Retry, Work Offline, and Cancel. If you choose Work Offline you are given this error message:

"---------  
Microsoft Office Outlook  
---------  
Outlook cannot log on. Verify you are connected to the network and are using the proper server and mailbox name. The connection to Microsoft Exchange is unavailable. Outlook must be online or connected to complete this action.  
---------  
OK  
---------  
"

then the opportunity to enter the server name to connect to:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-va9kZF2Ul5Y/Tz2Q1GOk_rI/AAAAAAAAAI4/7YE3_z4TvSM/s1600/choose-exchange-server.png"><img src="http://4.bp.blogspot.com/-va9kZF2Ul5Y/Tz2Q1GOk_rI/AAAAAAAAAI4/7YE3_z4TvSM/s400/choose-exchange-server.png" width="342" height="336" border="0" /></a>
</div>

If you correct the name here you will get an error where you can close Outlook then reopen it for it to operate. Another option is to change the default of that server to the correct one. I figured out the registry keys to do those so you can place in a GPO object.

Here are the keys:

> 
```plaintext
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Windows Messaging Subsystem\Profiles\ttye\8fa3465791d2b746a2c7d11ca063b282]
"001e660c"="outlook.company.corp"

[HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Windows Messaging Subsystem\Profiles\ttye\406169da1432914e8075fa26cbcdc53a]
"001e660c"="outlook.company.corp"

[HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Windows Messaging Subsystem\Profiles\ttye\1e8559cf990bb44792093b0cdfca0186]
"001e660c"="outlook.company.corp"

[HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Windows Messaging Subsystem\Profiles\ttye\13dbb0c8aa05101a9bb000aa002fc45a]
"001e6602"="outlook.company.corp"

[HKEY_CURRENT_USER\Software\Microsoft\Windows NT\CurrentVersion\Windows Messaging Subsystem\Profiles\ttye\dca740c8c042101ab4b908002b2fe182]
"001f662a"=hex:6f,00,75,00,74,00,6c,00,6f,00,6f,00,6b,00,2e,00,63,00,63,00,73,\
00,2e,00,63,00,6f,00,72,00,70,00,00,0
```


The last key is a REG_BINARY of the server name (outlook.company.corp). If we make this into a GPO object then these keys can be placed in and users can connect to Exchange without the messages above.

The GPO object needs to look like so:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-Cljnu6lB4Ao/Tz2RzzfI64I/AAAAAAAAAJE/6999E_Fa16I/s1600/choose-exchange-server.png"><img src="http://3.bp.blogspot.com/-Cljnu6lB4Ao/Tz2RzzfI64I/AAAAAAAAAJE/6999E_Fa16I/s400/choose-exchange-server.png" width="400" height="140" border="0" /></a>
</div>

Notice the %LogonUser% variable.

Each "Key Path" requires your username substituted for the %LogonUser% variable as well:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-jMiGriU4L7U/Tz2SQUbZlzI/AAAAAAAAAJQ/1M38oYBf-oc/s1600/choose-exchange-server.png"><img src="http://2.bp.blogspot.com/-jMiGriU4L7U/Tz2SQUbZlzI/AAAAAAAAAJQ/1M38oYBf-oc/s400/choose-exchange-server.png" width="400" height="180" border="0" /></a>
</div>

Set this GPO with a loopback processing setting and you're rolling. The negative that I've seen with this approach is that it will set the registry keys on a new login, but launching Outlook for the first time will overwrite them with the defaults set in the PRF. If you cancel out and relaunch the registry keys will apply again and the server you specified in them will work.

Or you can setup a DNS Alias, but this was an interesting exercise anyways 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->