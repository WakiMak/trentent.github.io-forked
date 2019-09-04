---
id: 1182
title: Group Creation Script
date: 2016-04-25T11:23:16-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/690-revision-v1/
permalink: /2016/04/25/690-revision-v1/
---
The final script:

<pre class="lang:batch decode:true ">:This script will create the proper domain local group and global groups
:The only input this script needs is the UNC path to the folder. At the end
:of this script the only thing you'll need to do is add the users from the actual
:folder to the global group.
:
:eg create-groups.cmd "\\file01\HR\HR Support Group\HR Team\HR Support Services"

SET /P Permissions=What permissions will this group have ([F]ULL/[M]ODIFIY/[RO]READ ONLY)?

IF /I '%PERMISSIONS%' EQU 'F' SET PERMISSIONS=FULL
IF /I '%PERMISSIONS%' EQU 'M' SET PERMISSIONS=MODIFY
IF /I '%PERMISSIONS%' EQU 'RO' SET PERMISSIONS=RO





:ECHO %1 |sed "s/\\\\//g"|sed "s/\\/\./g" | sed "s/\"//g" 

:We parse the command line for the UNC structure, now we need to find the last folder
for /F "tokens=*" %%A IN ('ECHO %1 ^|sed ^"s/\\\\//g^"^|sed ^"s/\\/\./g^" ^| sed ^"s/\^"//g^"') DO set groupname=%%A


set str=%groupname%
set N=0
setLocal EnableDELAYedExpansion

:loop
if !N! equ 55 (
goto :exceedcharacterquota
)

set /A N+=1

ECHO N=!N!
if "!str:~1!" neq "" (
set str=!str:~1!
goto :loop
)
goto :skip-string-modification

:if string length exceeds 55 chars, take the first 25 chars and the last 25 chars with an ellipse (...)
:in between.
:exceedcharacterquota
set string-part-one=!groupname:~0,25!
set string-part-two=!groupname:~-25!
set GROUPNAME=!string-part-one!...!string-part-two!


:skip-string-modification
setLocal disableDELAYedExpansion
:Remove any trailing spaces
for /F "tokens=*" %%A IN ('ECHO %GROUPNAME% ^|sed ^"s/ $//g^"') DO set groupname=%%A
ECHO GROUP=%GROUPNAME%

:Sets OU to domain local resource group...
SET OUL=OU=Resource,OU=Security Groups,OU=AD Project 3,DC=CCS,DC=CORP
dsadd group "CN=F.lg.%GROUPNAME%.%PERMISSIONS%,%OUL%" -desc %1 -secgrp yes -scope l

:Sets OU to Global group...
SET OUG=OU=Global,OU=Security Groups,OU=AD Project 3,DC=CCS,DC=CORP
dsadd group "CN=gg.%GROUPNAME%.%PERMISSIONS%,%OUG%" -desc %1 -secgrp yes -scope g

:adds the global group to the domain local group
dsmod group "CN=F.lg.%GROUPNAME%.%PERMISSIONS%,%OUL%" -addmbr "CN=gg.%GROUPNAME%.%PERMISSIONS%,%OUG%"</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->