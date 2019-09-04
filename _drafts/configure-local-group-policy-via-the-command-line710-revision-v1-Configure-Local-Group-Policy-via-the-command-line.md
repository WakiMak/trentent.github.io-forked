---
id: 1201
title: Configure Local Group Policy via the command line
date: 2016-04-25T11:52:46-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/710-revision-v1/
permalink: /2016/04/25/710-revision-v1/
---
Apply a security policy using an .inf file

[Unicode]Unicode=yes  
[Version]signature=&#8221;$CHICAGO$&#8221;  
Revision=1  
[Profile Description]  
Description=profile description  
[System Access]  
MinimumPasswordAge = 10  
MaximumPasswordAge = 30  
MinimumPasswordLength = 6  
RequireLogonToChangePassword = 0  
NewAdministratorName = &#8220;NewAdminAccountName&#8221;  
NewGuestName = &#8220;NewGuestAccountName&#8221;

Simple to apply from a cmd line

<pre class="lang:default decode:true ">secedit /configure /db %windir%\security\database\localdb.sdb /cfg %systemdrive%\install\local\policy\policyname.inf /verbose</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->