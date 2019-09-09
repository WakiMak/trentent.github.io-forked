---
id: 1722
title: AppV 5.1 Sequencer - Not capturing all registry keys
date: 2016-09-20T12:46:06-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1722
permalink: /2016/09/20/appv-5-1-sequencer-not-capturing-all-registry-keys/
categories:
  - Blog
tags:
  - AppV
  - PowerShell
  - scripting
---
Video of this issue:

[https://theorypc-my.sharepoint.com/personal/trententtye\_theorypc\_onmicrosoft\_com/\_layouts/15/guestaccess.aspx?guestaccesstoken=vq4pmhseZam8zGPBh6Q8bn%2bgvaZrVuFc4fXo%2fziYmeA%3d&docid=08e53da35fa314929a7ce6578c69bf5c5](https://theorypc-my.sharepoint.com/personal/trententtye_theorypc_onmicrosoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=vq4pmhseZam8zGPBh6Q8bn%2bgvaZrVuFc4fXo%2fziYmeA%3d&docid=08e53da35fa314929a7ce6578c69bf5c5)

The issue is when sequencing an application (100% reproducable on Epic and the VMWare Hypervisor) and then you add a large 'update' (for Epic this is the client pack) then not all registry keys are captured.  At 8:20 seconds you can see keys that are present in the local registry are not present in the package.

<img class="aligncenter size-full wp-image-1723" src="http://theorypc.ca/wp-content/uploads/2016/09/AppV_Bug.png" alt="appv_bug" width="586" height="826" srcset="http://theorypc.ca/wp-content/uploads/2016/09/AppV_Bug.png 586w, http://theorypc.ca/wp-content/uploads/2016/09/AppV_Bug-213x300.png 213w" sizes="(max-width: 586px) 100vw, 586px" /> 

&nbsp;

Notice the "0", "win32" and FLAGS keys are missing in the AppV package.

This is the script I used to compare the local registry vs the package:

<pre class="lang:ps decode:true ">pushd
#mkdir "$env:userprofile\desktop\Epic_Hyperspace_2014_8.1_RA1504_CP12_x86"
New-AppvSequencerPackage -Name "Epic_Hyperspace_2014_8.1_RA1504_CP12_x86" -OutputPath "$env:userprofile\desktop" -FullLoad -Installer "AppV-2014_Hyperspace_Install.bat" 

#Extract registry hive
cd "$env:userprofile\desktop"
Add-Type -A 'System.IO.Compression.FileSystem'
[IO.Compression.ZipFile]::ExtractToDirectory(''+$env:userprofile+'\desktop\Epic_Hyperspace_2014_8.1_RA1504_CP12_x86\Epic_Hyperspace_2014_8.1_RA1504_CP12_x86.appv', ''+$env:userprofile+'\Desktop\package')

reg load HKLM\PE_REG "$env:userprofile\Desktop\package\Registry.dat"

#compare Classes keys
cd HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes
$appvKeys = dir
"SOFTWARE\Classes" | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt

foreach ( $KeyPath in $appvKeys )
{

    $keyName = $KeyPath.PSChildName
    $PE_REGCount =  (dir HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\$keyname).Count

    $compareKey = (dir HKLM:\SOFTWARE\Classes\$KeyName).Count
   # write-host $PE_REGCount $compareKey
    if (compare-object $PE_REGCount $compareKey) { 
    $KeyName
    $keyName | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt
    }
}


##compare TypeLibs
cd HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\TypeLib
$appvKeys = dir

"SOFTWARE\Classes\TypeLib" | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt

foreach ( $KeyPath in $appvKeys )
{

    $keyName = $KeyPath.PSChildName
    $PE_REGCount =  (dir -recurse HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\TypeLib\$keyname).subkeyCount

    $compareKey = (dir -recurse HKLM:\SOFTWARE\Classes\TypeLib\$KeyName).subkeyCount
   # write-host $PE_REGCount $compareKey
    if (compare-object $PE_REGCount $compareKey) { 
    #write-host $PE_REGCount "   vs  "  $compareKey
    write-host $KeyName
    $keyName | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt
    }
}

##compare Wow6432Node Classes
cd HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Wow6432Node\CLSID
$appvKeys = dir

"SOFTWARE\Classes\Wow6432Node\CLSID" | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt

foreach ( $KeyPath in $appvKeys )
{

    $keyName = $KeyPath.PSChildName
    $PE_REGCount =  (dir  HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Wow6432Node\CLSID\$keyname).subkeyCount

    $compareKey = (dir  HKLM:\SOFTWARE\Classes\Wow6432Node\CLSID\$KeyName).subkeyCount
   # write-host $PE_REGCount $compareKey
    if (compare-object $PE_REGCount $compareKey) { 
    #write-host $PE_REGCount "   vs  "  $compareKey
    write-host $KeyName
    $keyName | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt
    }
}

##compare Wow6432Node Interface
cd HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Wow6432Node\Interface
$appvKeys = dir

"SOFTWARE\Classes\Wow6432Node\Interface" | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt

foreach ( $KeyPath in $appvKeys )
{

    $keyName = $KeyPath.PSChildName
    $PE_REGCount =  (dir -recurse HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Wow6432Node\Interface\$keyname).subkeyCount

    $compareKey = (dir -recurse HKLM:\SOFTWARE\Classes\Wow6432Node\Interface\$KeyName).subkeyCount
   # write-host $PE_REGCount $compareKey
    if (compare-object $PE_REGCount $compareKey) { 
    #write-host $PE_REGCount "   vs  "  $compareKey
    write-host $KeyName
    $keyName | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt
    }
}

##compare Interface
cd HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Interface
$appvKeys = dir

"SOFTWARE\Classes\Interface" | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt

foreach ( $KeyPath in $appvKeys )
{

    $keyName = $KeyPath.PSChildName
    $PE_REGCount =  (dir -recurse HKLM:\PE_REG\REGISTRY\MACHINE\SOFTWARE\Classes\Interface\$keyname).subkeyCount

    $compareKey = (dir -recurse HKLM:\SOFTWARE\Classes\Interface\$KeyName).subkeyCount
   # write-host $PE_REGCount $compareKey
    if (compare-object $PE_REGCount $compareKey) { 
    #write-host $PE_REGCount "   vs  "  $compareKey
    write-host $KeyName
    $keyName | out-file -append -noclobber $env:userprofile\Desktop\mismatched_registry_entry.txt
    }
}

reg unload HKLM\PE_REG</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->