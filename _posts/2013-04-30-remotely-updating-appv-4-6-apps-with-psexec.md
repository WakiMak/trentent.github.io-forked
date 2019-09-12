---
id: 650
title: Remotely updating AppV 4.6 apps with PSEXEC
date: 2013-04-30T11:46:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/04/30/remotely-updating-appv-4-6-apps-with-psexec/
permalink: /2013/04/30/remotely-updating-appv-4-6-apps-with-psexec/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/04/remotely-updating-appv-46-apps-with.html
blogger_internal:
  - /feeds/7977773/posts/default/2775055525624982744
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - scripting
---
Just create a script file (flush.cmd) that looks like so:

```shell
sftmime delete app:"AppName" 
sftmime refresh server:"AppVManagementServer" 
sftmime load app:"AppName" 
sftmime query obj:package
```

and you can execute the command with this PSEXEC.exe command:  
<span style="font-family: 'Courier New',Courier,monospace;"><br /> </span>


```plaintext
for /f %A IN ('type C:\Users\trententtye\Desktop\list.txt') 
 DO P:\PSTools\PsExec.exe \\%A -c C:\Users\trententtye\Desktop\flush.cm
```


With list.txt looking like so:

<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER1</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER2</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER3</span>  
<span style="font-family: 'Courier New',Courier,monospace;">APPSERVER4</span>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->