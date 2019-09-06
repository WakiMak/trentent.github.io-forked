---
id: 672
title: Creating a batch file that accepts parameters
date: 2012-10-24T10:54:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/10/24/creating-a-batch-file-that-accepts-parameters/
permalink: /2012/10/24/creating-a-batch-file-that-accepts-parameters/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/10/creating-batch-file-that-accepts.html
blogger_internal:
  - /feeds/7977773/posts/default/8450044311113861233
categories:
  - Blog
  - Uncategorized
tags:
  - scripting
---
We are App-V'ing a bunch of our applications and one of the applications requires a different .ini file depending if it's test, production or training. &nbsp;To get around creating 3 different packages with just a single file, I've written a pre-launch batch file that will auto-create the .ini depending on the parameters passed to it.

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:===========================================================================================</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; Paris Pre-launch script for App-V</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; By Trentent Tye</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; 2012-10-24</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; This command file will take numerous parameters for launching the specific type</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; of Paris application. &nbsp;It will autogenerate an INI file that Paris uses on</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; launch to self configure. &nbsp;If no parameter is passed, it's assumed to be</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; commented out in the INI file.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:&nbsp;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; This file should be launched like so:</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; paris.cmd PARA request=0 hostname=nice servergroup=development_3861</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; it will automatically find the variable without an equal sign and assume</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; that it is the alias. &nbsp;Everything else gets matched up to the [Host]</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; section of the .ini</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:1) Pull string off the command line</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:2) filter out " and '</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:3) break each set out by spaces</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:4) save to temp file</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:5) iterate through temp file (for) and create variables.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">@ECHO OFF</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Launching Paris</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">SET PARAMS=%*</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:NOTE: We are replacing quotations with whitespace characters! &nbsp;Ensure there is no</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:whitespace in the parameters, but whitespace *between* parameters.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">call set PARAMS=%%PARAMS:"= %%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">call set PARAMS=%%PARAMS:'= %%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %PARAMS%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:Break each set of parameters into it's own set seperated by the equals sign.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:If there is no parameter stored we "breakout"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">for /f "tokens=1-26 delims= " %%A IN ("%PARAMS%") DO (</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%A' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%A > "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%B' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%B >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%C' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%C >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%D' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%D >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%E' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%E >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%F' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%F >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%G' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%G >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%H' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%H >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%I' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%I >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%J' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%J >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%K' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%K >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF '%%L' EQU " GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%L >> "%TEMP%variables.txt"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">)</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:Now we iterate through the temp file and auto-create the Paris.ini</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">for /f "tokens=1-2 delims==" %%A IN ('TYPE "%TEMP%variables.txt"') DO (</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'REQUEST' set REQUEST=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'OBJECTNAME' set OBJECTNAME=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'HOSTNAME' set HOSTNAME=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'REQUESTBROKER' set REQUESTBROKER=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'RequestBrokerBackup' set RequestBrokerBackup=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%A' EQU 'ServerGroup' set ServerGroup=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I '%%B' EQU " set ALIAS=%%A</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">)</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:Now that we have all the parameters for the ini file we can create it.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">SET PARISPATH="%TEMP%paris.ini"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [PreLogin] >"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ClientDLL=STAPPClientLogin >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO FunctionName=DoLogin >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ServerDLL=STServerLogin >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO DataModule=Login >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [PostLogin] >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ServerDLL=STSRVLogin >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO DataModule=Login >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Timer] >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Enabled=0 >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Interval=10000 >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Host] >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%REQUEST%" EQU "" ECHO #Request=0 >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%REQUEST%" NEQ "" ECHO Request=%REQUEST% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%OBJECTNAME%" EQU "" ECHO #objectname=I4AppServer >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%OBJECTNAME%" NEQ "" ECHO objectname=%OBJECTNAME% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%HOSTNAME%" EQU "" ECHO #hostname=AComputerName >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%HOSTNAME%" NEQ "" ECHO hostname=%HOSTNAME% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%REQUESTBROKER%" EQU "" ECHO #RequestBroker=AComputerName >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%REQUESTBROKER%" NEQ "" ECHO RequestBroker=%REQUESTBROKER% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%RequestBrokerBackup%" EQU "" ECHO #RequestBrokerBackup=AnotherComputerName >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%RequestBrokerBackup%" NEQ "" ECHO RequestBrokerBackup=%RequestBrokerBackup% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%ServerGroup%" EQU "" ECHO #ServerGroup=AnotherComputerName >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I "%ServerGroup%" NEQ "" ECHO ServerGroup=%ServerGroup% >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Settings] >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO MaxLookup=200 >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO NotifyInterval=30000 >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO #Interval between refresh of InBox screen (1second=1000) >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Helpfile=paris1.chm >>"%PARISPATH%"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:ADD COPY COMMAND</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:ADD LAUNCH COMMAND</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:copy /y "%PARISPATH%" "B:In4tek_Paris_37_MNTParisbinSTAPPParisShellDCOM.INI"</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO "B:In4tek_Paris_37_MNTParisbinSTAPPParisShellDCOM.exe -alias %ALIAS%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">type "%PARISPATH%"</span>
</div>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->