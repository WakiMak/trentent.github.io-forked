---
id: 1187
title: 'Saving and restoring ACL&#8217;s on OU&#8217;s'
date: 2016-04-25T11:34:55-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/696-autosave-v1/
permalink: /2016/04/25/696-autosave-v1/
---
Saving and moving OU ACLs

I’ve written a batch file that will move ACLs from one OU to another. It works by you outputting the results of a ACL from a OU to a text file, specifying the new OU in a batch file and inputting the text file you just created. I use three utilities to accomplish this: adfind.exe, sed.exe and dsacls.exe.  
The command to save the text file is:

> <pre class="lang:batch decode:true ">adfind -b "OU=Users,OU=LAB,DC=LAB,DC=CORP" -f (distinguishedName=OU=Users,OU=LAB,DC=LAB,DC=corp) -sddl++ -resolvesids -onlydacl ntsecuritydescriptor -sddlnotfilter ;inherited| sed.exe "s/;;/; ;/g" | sed.exe "s/;;/; ;/g" | sed.exe "s/;;/; ;/g" | sed.exe "s/;;/; ;/g" &gt; %PATHTOFILE%.txt</pre>

From here, you need to delete the header in the text file and the footer.  
Once that is done, run this script, changing the two variables at the top:

> <pre class="lang:batch decode:true ">:RESTORE-OU-ACL.CMD
:Restore OU Properties
SET TARGETOU=OU=Users Accounts,OU=AD Project 3,DC=LAB,DC=CORP
SET TARGETFILE="New Text Document (5).txt"

@ECHO OFF

SETLOCAL ENABLEDELAYEDEXPANSION
for /F "tokens=1-6 delims=;" %%A IN ('type %TARGETFILE%') DO (
SET PROP=
SET INHERIT=0
IF "%%C" EQU " " SET PROP=GA
ECHO CALL :PROPERTYACL %%C
CALL :PROPERTYACL %%C

ECHO CALL :INHERITANCE %%B
CALL :INHERITANCE %%B

SET PROPERTY=
IF /I "%%D" NEQ " " SET PROPERTY=%%D
ECHO PROPERTY=!PROPERTY!
SET TARGET=
IF /I "%%E" NEQ " " SET TARGET=%%E
ECHO TARGET=!TARGET!
ECHO dsacls "%TARGETOU%" !INHERIT! /G "%%F:!PROP!;!PROPERTY!;!TARGET!"
dsacls "%TARGETOU%" !INHERIT! /G "%%F:!PROP!;!PROPERTY!;!TARGET!"

)
GOTO:EOF

:INHERITANCE
REM We need to figure out what ACLS we're dealing with...
FOR /F "tokens=*" %%Z IN ('ECHO %*') DO (
IF '!INHERIT!' EQU '/I:S' GOTO:EOF
ECHO %%Z | FINDSTR /I /C:"[CONT INHERIT]"
IF '!ERRORLEVEL!' EQU '0' SET INHERIT=/I:T
ECHO %%Z | FINDSTR /I /C:"[CONT INHERIT][INHERIT ONLY]"
IF '!ERRORLEVEL!' EQU '0' SET INHERIT=/I:S
ECHO %%Z | FINDSTR /I /C:"INHERIT"
IF '!ERRORLEVEL!' EQU '1' SET INHERIT=/I:P
ECHO INHERIT=!INHERIT!
)
GOTO:EOF

:PROPERTYACL
REM We need to figure out what ACLS we're dealing with...
FOR /F "tokens=*" %%Z IN ('ECHO %*') DO (
ECHO %%Z | FINDSTR /I /C:"WRT PROP"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!WP
ECHO %%Z | FINDSTR /I /C:"READ PROP"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!RP
ECHO %%Z | FINDSTR /I /C:"CTL"
IF '!ERRORLEVEL!' EQU '0' SET PROP=CA
ECHO %%Z | FINDSTR /I /C:"[CR CHILD]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!CC
ECHO %%Z | FINDSTR /I /C:"[DEL CHILD]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!DC
ECHO %%Z | FINDSTR /I /C:"[LIST CHILDREN]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!LC
ECHO %%Z | FINDSTR /I /C:"[LIST OBJECT]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!LO
ECHO %%Z | FINDSTR /I /C:"[READ]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!GR
ECHO %%Z | FINDSTR /I /C:"[FC]"
IF '!ERRORLEVEL!' EQU '0' SET PROP=!PROP!GA

ECHO PROP=!PROP!
)
GOTO:EOF

:/I:P = This Object Only *BLANK*
:/I:S = Child Objects Only [CONT INERIT][INHERIT ONLY]
:/I:T = This object and all child objects [CONT INERIT]
:Blank inheritance = /I:P
:When "Properties" are set, it should be /I:S
:When there are no properties listed at all ACL should be GA</pre>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->