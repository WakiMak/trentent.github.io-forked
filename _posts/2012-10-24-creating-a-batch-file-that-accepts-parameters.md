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
We are App-V&#8217;ing a bunch of our applications and one of the applications requires a different .ini file depending if it&#8217;s test, production or training. &nbsp;To get around creating 3 different packages with just a single file, I&#8217;ve written a pre-launch batch file that will auto-create the .ini depending on the parameters passed to it.

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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">: &nbsp; &nbsp; &nbsp; &nbsp; launch to self configure. &nbsp;If no parameter is passed, it&#8217;s assumed to be</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:2) filter out &#8221; and &#8216;</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">call set PARAMS=%%PARAMS:&#8221;= %%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">call set PARAMS=%%PARAMS:&#8217;= %%</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:Break each set of parameters into it&#8217;s own set seperated by the equals sign.</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:If there is no parameter stored we &#8220;breakout&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">for /f &#8220;tokens=1-26 delims= &#8221; %%A IN (&#8220;%PARAMS%&#8221;) DO (</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%A&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%A > &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%B&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%B >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%C&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%C >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%D&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%D >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%E&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%E >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%F&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%F >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%G&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%G >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%H&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%H >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%I&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%I >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%J&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%J >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%K&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%K >> &#8220;%TEMP%variables.txt&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF &#8216;%%L&#8217; EQU &#8221; GOTO BREAKOUT</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO %%L >> &#8220;%TEMP%variables.txt&#8221;</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">for /f &#8220;tokens=1-2 delims==&#8221; %%A IN (&#8216;TYPE &#8220;%TEMP%variables.txt&#8221;&#8216;) DO (</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;REQUEST&#8217; set REQUEST=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;OBJECTNAME&#8217; set OBJECTNAME=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;HOSTNAME&#8217; set HOSTNAME=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;REQUESTBROKER&#8217; set REQUESTBROKER=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;RequestBrokerBackup&#8217; set RequestBrokerBackup=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%A&#8217; EQU &#8216;ServerGroup&#8217; set ServerGroup=%%B</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">IF /I &#8216;%%B&#8217; EQU &#8221; set ALIAS=%%A</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">SET PARISPATH=&#8221;%TEMP%paris.ini&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [PreLogin] >&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ClientDLL=STAPPClientLogin >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO FunctionName=DoLogin >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ServerDLL=STServerLogin >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO DataModule=Login >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [PostLogin] >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO ServerDLL=STSRVLogin >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO DataModule=Login >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Timer] >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Enabled=0 >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Interval=10000 >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Host] >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%REQUEST%&#8221; EQU &#8220;&#8221; ECHO #Request=0 >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%REQUEST%&#8221; NEQ &#8220;&#8221; ECHO Request=%REQUEST% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%OBJECTNAME%&#8221; EQU &#8220;&#8221; ECHO #objectname=I4AppServer >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%OBJECTNAME%&#8221; NEQ &#8220;&#8221; ECHO objectname=%OBJECTNAME% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%HOSTNAME%&#8221; EQU &#8220;&#8221; ECHO #hostname=AComputerName >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%HOSTNAME%&#8221; NEQ &#8220;&#8221; ECHO hostname=%HOSTNAME% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%REQUESTBROKER%&#8221; EQU &#8220;&#8221; ECHO #RequestBroker=AComputerName >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%REQUESTBROKER%&#8221; NEQ &#8220;&#8221; ECHO RequestBroker=%REQUESTBROKER% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%RequestBrokerBackup%&#8221; EQU &#8220;&#8221; ECHO #RequestBrokerBackup=AnotherComputerName >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%RequestBrokerBackup%&#8221; NEQ &#8220;&#8221; ECHO RequestBrokerBackup=%RequestBrokerBackup% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%ServerGroup%&#8221; EQU &#8220;&#8221; ECHO #ServerGroup=AnotherComputerName >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">if /I &#8220;%ServerGroup%&#8221; NEQ &#8220;&#8221; ECHO ServerGroup=%ServerGroup% >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;"><br /></span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO. >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO [Settings] >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO MaxLookup=200 >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO NotifyInterval=30000 >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO #Interval between refresh of InBox screen (1second=1000) >>&#8221;%PARISPATH%&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO Helpfile=paris1.chm >>&#8221;%PARISPATH%&#8221;</span>
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
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">:copy /y &#8220;%PARISPATH%&#8221; &#8220;B:In4tek_Paris_37_MNTParisbinSTAPPParisShellDCOM.INI&#8221;</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">ECHO &#8220;B:In4tek_Paris_37_MNTParisbinSTAPPParisShellDCOM.exe -alias %ALIAS%</span>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: xx-small;">type &#8220;%PARISPATH%&#8221;</span>
</div>

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->