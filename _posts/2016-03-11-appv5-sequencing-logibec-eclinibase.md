---
id: 527
title: AppV5 - Sequencing Logibec eClinibase
date: 2016-03-11T17:04:00-06:00
author: trententtye
layout: post
permalink: /2016/03/11/appv5-sequencing-logibec-eclinibase/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/03/appv5-sequencing-logibec-eclinibase.html
blogger_internal:
  - /feeds/7977773/posts/default/8800929600389540569
image: /wp-content/uploads/2016/03/1-7.png
categories:
  - Blog
tags:
  - AppV
---
I was given a new application to sequence:  
Logibec's eClinibase.

This application has some restrictions:  
1) It has an updater that will pull down .exe's  
2) It won't launch from a script in the bubble.  Example:  
cmd.exe /appvve:%PACKAGEGUID%_%VERSIONGUID%  
cd /d C:LogibeceClinibaseilgi11  
eclinibase.exe

Will crash.

I'm going to address point 2) first because it's weird and interesting.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="/wp-content/uploads/2016/03/1-7.png"><img src="/wp-content/uploads/2016/03/1-7-300x118.png" width="320" height="125" border="0" /></a>
</div>

Why does it crash?  Because, somehow, it breaks out of the bubble.  The reason it crashes is it looking for some registry keys that don't exist outside the bubble and it explodes.  The keys it looks for are:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="/wp-content/uploads/2016/03/3-5.png"><img src="/wp-content/uploads/2016/03/3-5-300x64.png" width="320" height="67" border="0" /></a>
</div>

On a local install if I delete the environment keys that matchup to the folder we're launching eClinibase from, I get the same error.  Since those keys don't exist outside the bubble, we get the same error...  Which means it's trying to look outside the bubble for some reason.  Powershell, however, works correctly but only if the exe is called directly:

```shell
powershell.exe  -executionPolicy byPass -command "&{Start-AppvVirtualProcess -AppvClientObject(Get-AppvClientPackage Logibec_eClinibase_910.0.3.3_x86) C:\LOGIBEC\eClinibase\ilgi11\eClinibase.exe}"
```

However, we CAN get it to stay 'in the bubble' if we launch the eClinibase.exe with the /appvve: switch.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="/wp-content/uploads/2016/03/4-3.png"><img src="/wp-content/uploads/2016/03/4-3-300x117.png" width="320" height="124" border="0" /></a>
</div>

This is the first time I've ever encountered this where launching the application from a command prompt inside the AppV bubble fails, but it will work if you launch the exe with the /appvve: switch.

So that's weird that we need to launch it specifically that way, but it appears to work so I guess that's a limitation we have to roll with.

So how do we address point 1)?

I've used this technique before and it works here.  We will create a directory symlink to a network file share where the users will have full permission to update the application.  Since these updates occur 'as needed' the different environments may update at different times, this keeps them in sync across all of our Citrix servers since they will all point to the same location.

I add the mklink command the DynamicDeploymentConfig.xml:


```xml
<!-- Machine Scripts Example - customize and uncomment to use machine scripts>
 
<machinescripts>
   
  <addpackage>
    <path>C:\Windows\System32\cmd.exe</path>
    <arguments>/c mklink /D C:\LOGIBEC \\citrixnas01\CTX_APPS\LOGIBEC</arguments>
    <wait rollbackonerror=""false"" timeout=""30"/">
  </wait></addpackage>
  <removepackage>
    <path>C:\Windows\System32\cmd.exe</path>
    <arguments>/c rmdir /q C:\LOGIBEC</arguments>
    <wait rollbackonerror=""false"" timeout=""60"/">
  </wait></removepackage>
</machinescripts>
 
<!--
   
  Terminate Child Processes
   
  -->
<terminatechildprocesses>
  <!--
        <application Path="[{AppVPackageRoot}]\Contoso\ContosoApp.EXE" />
      -->
</terminatechildprocesses>
```


And we are good to go!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->