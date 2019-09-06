---
id: 703
title: Find all OU's and what GPO's are linked to them
date: 2011-07-11T11:11:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/07/11/find-all-ous-and-what-gpos-are-linked-to-them/
permalink: /2011/07/11/find-all-ous-and-what-gpos-are-linked-to-them/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/07/find-all-ous-and-what-gpos-are-linked.html
blogger_internal:
  - /feeds/7977773/posts/default/6041090974051227424
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - Group Policy
  - scripting
---
I made a script using SED and ADFIND to find all OU's and what GPO's were linked to them:

> <pre class="lang:batch decode:true ">adfind -b DC=ccs,DC=corp -f "(&(objectCategory=organizationalUnit)(gPLink=*))" cn gplink -csv -csvdelim : > gplinks.txt
adfind -b DC=ccs,DC=corp -f "(&(objectCategory=groupPolicyContainer)(cn=*))" cn displayName -nodn -csv > gpnames.txt
sed -i "s/\"//g" gpnames.txt
sed -i "s/\[LDAP:\/\/..=//g" gplinks.txt
sed -r -i "s/..=.olicies,..=.ystem,DC=ccs,DC=corp\\;.\]//g" gplinks.txt

for /f "tokens=1-2 delims=," %A IN ('type "gpnames.txt"') DO sed -i "s/%A/%B/g" "gplinks.txt"
del sed* /q</pre>

Love it 🙂

To expand on the above, here is a batch file that will find all empty OU's and what GPO's are linked to them:

> <pre class="lang:batch decode:true ">:This script requires SED.txt and ADFIND.exe

:Goes through every OU and finds every GPO linked to it
adfind -b DC=ccs,DC=corp -f "(&(objectCategory=organizationalUnit)(gPLink=*))" cn gplink -csv -csvdelim : > gplinks.txt
:Goes through every GPO and finds it's common name
adfind -b DC=ccs,DC=corp -f "(&(objectCategory=groupPolicyContainer)(cn=*))" cn displayName -nodn -csv > gpnames.txt

:run some text clean up commands. Deletes double-quotes
sed -i "s/\"//g" gpnames.txt
:run some text clean up commands. Deletes [LDAP://
sed -i "s/\[LDAP:\/\/..=//g" gplinks.txt
:run some text clean up commands.
sed -r -i "s/..=.olicies,..=.ystem,DC=ccs,DC=corp\\;.\]//g" gplinks.txt
pause
:repalces the LDAP GUID name in gplinks.txt with the actual name
for /f "tokens=1-2 delims=," %%A IN ('type "gpnames.txt"') DO sed -i "s/%%A/%%B/g" "gplinks.txt"
sed -i "s/\"//g" gplinks.txt

:Finds all empty OU's
@Echo Off
SETLOCAL EnableDelayedExpansion

IF EXIST EmptyOUs.txt DEL /F /Q EmptyOUs.txt
SET OUQry=DSQuery OU -Name * -Limit 0
FOR /F "delims=#" %%o IN ('%OUQry%') Do (
Echo Processing: %%o
DSQuery * %%o -Limit 0 | Find "CN=" >NUL
IF ERRORLEVEL 1 Echo %%o>>EmptyOUs.txt
)
Echo.
Echo Search Complete! Check 'EmptyOUs.txt' file.
Echo.
ENDLOCAL
sed -i "s/\"//g" EmptyOUs.txt


:if gplinks OU is equal to an OU in EmptyOU then replace the OU with EMPTY:OU
for /f "tokens=1-10 delims=:" %%A IN ('type gplinks.txt') DO (
for /f "tokens=*" %%a IN ('type EmptyOUs.txt') DO (
if /I %%A equ %%a sed -i "s/%%A/EMPTY:%%A/g" gplinks.txt
)
)
del sed* /q</pre>
> 
> &nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->