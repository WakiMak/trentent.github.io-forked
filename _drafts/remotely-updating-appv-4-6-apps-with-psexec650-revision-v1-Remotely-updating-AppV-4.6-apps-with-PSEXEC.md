---
id: 1116
title: Remotely updating AppV 4.6 apps with PSEXEC
date: 2016-04-25T08:25:53-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/650-revision-v1/
permalink: /2016/04/25/650-revision-v1/
---
Just create a script file (flush.cmd) that looks like so:

<pre class="lang:batch decode:true ">sftmime delete app:"AppName" 
sftmime refresh server:"AppVManagementServer" 
sftmime load app:"AppName" 
sftmime query obj:package</pre>

and you can execute the command with this PSEXEC.exe command:  
<span style="font-family: 'Courier New',Courier,monospace;"><br /> </span>

<pre class="lang:default decode:true ">for /f %A IN ('type C:\Users\trententtye\Desktop\list.txt') 
 DO P:\PSTools\PsExec.exe \\%A -c C:\Users\trententtye\Desktop\flush.cmd</pre>

With list.txt looking like so:

<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER1</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER2</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER3</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER4</span>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->