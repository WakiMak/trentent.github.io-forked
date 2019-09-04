---
id: 1081
title: Error launching batch file from within AppV 5 bubble
date: 2016-04-25T07:34:42-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/622-autosave-v1/
permalink: /2016/04/25/622-autosave-v1/
---
So we have an application that requires the &#8220;%CLIENTNAME%&#8221; variable to be passed to it in it&#8217;s exe string. Â The string looks like so:

<div>
  <pre class="lang:default decode:true ">prowin32.exe -p \\nas\cfgstart.p -param S,%CLIENTNAME%,120n,citrix,a92,10920 -wy</pre>
</div>

<div>
</div>

<div>
  The issue we have is APPV does not seem to get that variable and pass it to the program. Â So when the program starts, it makes %clientname% folders in a temp directory and we can&#8217;t have two %clientname% folders in the same directory so only one instance of the application can be launched *period* if we do it this way, as opposed to one per server.
</div>

<div>
</div>

<div>
  To resolve this issue I wrote a script that will pass the %CLIENTNAME% variable to AppV by ripping it out of the registry:
</div>

<div>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">=======================================================</span>
</div>

<div>
  <div>
    <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">ECHO Launching Centricity&#8230;</span>
  </div>
  
  <div>
    <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Â </span>
  </div>
  
  <div>
    <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">for /f &#8220;tokens=1-3&#8221; %%A IN (&#8216;reg query &#8220;HKCUvolatile environment&#8221; /s ^| findstr /i /c:&#8221;CLIENTNAME&#8221;&#8216;) DO SET CLIENTNAME=%%C</span>
  </div>
  
  <div>
    <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Â </span>
  </div>
  
  <div>
    <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">prowin32.exe -p \nascfgstart.p -param S,%CLIENTNAME%,120n,citrix,a92,10920 -wy</span>
  </div>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">=======================================================</span>
</div>

<div>
</div>

<div>
  This worked for AppV 4.6 without issue. Â Now with AppV 5 I get an error, PATH NOT FOUND when trying to launch this script.</p> 
  
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://2.bp.blogspot.com/-pcAv4kNar4A/UoaUgP-y9bI/AAAAAAAAAa0/5rt17WcaMhA/s1600/Screen+Shot+2013-11-15+at+2.38.12+PM.png"><img src="http://2.bp.blogspot.com/-pcAv4kNar4A/UoaUgP-y9bI/AAAAAAAAAa0/5rt17WcaMhA/s320/Screen+Shot+2013-11-15+at+2.38.12+PM.png" width="320" height="29" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    To verify the path exists in the app I ran the following commands:
  </p>
</div>

<div>
  <div style="clear: both; text-align: center;">
    <a style="margin-left: 1em; margin-right: 1em; text-align: center;" href="http://3.bp.blogspot.com/-T97P6fP8v4s/UoaUhHhfdvI/AAAAAAAAAbA/euNanlROOQ0/s1600/Screen+Shot+2013-11-15+at+2.38.03+PM.png"><img src="http://3.bp.blogspot.com/-T97P6fP8v4s/UoaUhHhfdvI/AAAAAAAAAbA/euNanlROOQ0/s320/Screen+Shot+2013-11-15+at+2.38.03+PM.png" width="320" height="237" border="0" /></a>
  </div>
  
  <div style="clear: both; text-align: center;">
  </div>
  
  <p>
    The powershell commands put me in the AppV 5 bubble then opened a command prompt. Â From the command prompt I can see the directory that is missing. Â Going back to procmon I was curious to see what command it was launching. Â It was launching this:
  </p>
</div>

<div>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">cmd /c &#8220;&#8221;C:ProgramDataMicrosoftAppVClientIntegration3230251A-5B8E-47EF-8378-986B2A492D05RootVFSCommon DesktopMyAppsBDM Pharmacy v9.2Centricity Pharmacy Test on rxpv91cal.cmd&#8221; /appvve:3230251A-5B8E-47EF-8378-986B2A492D05_03CA3F94-F318-4693-A7E3-038DB30E6C70&#8243;</span>
</div>

<div>
</div>

<div>
  This command was failing. Â It appears that when you are launching the .cmd file directly AppV 5 starts the cmd.exe *outside* the AppV bubble and it doesn&#8217;t connect to the appvve. Â To correct this I tried this command line:
</div>

<div>
</div>

<div>
  <span style="font-family: Courier New, Courier, monospace; font-size: x-small;">cmd /c <span style="color: red;">&#8220;cmd.exe /c &#8220;</span>C:ProgramDataMicrosoftAppVClientIntegration3230251A-5B8E-47EF-8378-986B2A492D05RootVFSCommon DesktopMyAppsBDM Pharmacy v9.2Centricity Pharmacy Test on rxpv91cal.cmd&#8221; /appvve:3230251A-5B8E-47EF-8378-986B2A492D05_03CA3F94-F318-4693-A7E3-038DB30E6C70&#8243;</span>
</div>

<div>
</div>

<div>
  Success! Â It launched successfully and saw the directory and everything was good there after. Â So let that be a lesson to other AppV 5 package makers, if you need a pre-launch script you may need to modify your published icon to put another cmd.exe /c before the command file for it to start in the bubble.</p> 
  
  <p>
    <a href="http://blogs.technet.com/b/virtualvibes/archive/2013/10/17/the-issues-of-sequencing-bat-shortcuts-in-app-v-5-0.aspx">A very good AppV blog</a> has already discovered this issue and came back with a better fix than mine:<br /> http://blogs.technet.com/b/virtualvibes/archive/2013/10/17/the-issues-of-sequencing-bat-shortcuts-in-app-v-5-0.aspx</div> <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->