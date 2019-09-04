---
id: 1171
title: Creating shares remotely using PowerShell
date: 2016-04-25T11:07:12-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/678-revision-v1/
permalink: /2016/04/25/678-revision-v1/
---
I&#8217;ve had a bit of a battle getting PowerShell to work on creating remote shares with the permissions I want. I think I have it working now in a fairly minimalist fashion.  
<span style="font-family: monospace;"><br /> </span>

<div style="margin-bottom: 0.0001pt;">
  <p>
    <span style="color: #007500; font-family: Consolas; font-size: 10.0pt;"><span style="color: #007500; font-family: Consolas; font-size: 10.0pt;">Â </span></span>
  </p>
  
  <pre class="lang:ps decode:true ">    #create a share using WMI and PowerShell
    #
    #5/14/2012 - By Trentent Tye
    #
    #To create a share with PowerShell utilizing WMI (so you don't need
    #to use PSRemoting) you need to do the following:
    #1) Create the Win32_Share class
    #2) Create the Security Descriptor for the share
    #3) Create the ACE for the share
    #4) Create the Trustee fo rthe ACE
    #5) Set all the variables
    #6) Create the share.
    #
    #The next lines sets a computer (%cn%) to "EVERYONE FULL CONTROL" on the
    #share "HomeDirs"

    $cshare = [WMIClass]"\\%cn%\root\cimv2:Win32_Share"
    $securityDescriptor = ([WMIClass] "\\%cn%\root\cimv2:Win32_SecurityDescriptor").CreateInstance()
    $ACE = ([WMIClass] "\\%cn%\root\cimv2:Win32_ACE").CreateInstance()
    $Trustee = ([WMIClass] "\\%cn%\root\cimv2:Win32_Trustee").CreateInstance()
    $Trustee.Name = "EVERYONE"
    $Trustee.Domain = $Null
    $Trustee.SID = @(1, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0)
    $ace.AccessMask = 2032127
    $ace.AceFlags = 3
    $ace.AceType = 0
    $ACE.Trustee = $Trustee
    $securityDescriptor.DACL += $ACE.psObject.baseobject

    #trying to create share...  variables are:
    #,,,(if $Null set to maximum allowed),,,
    $result = $cshare.create("%homeDrive%:\homedirs","homedirs",0,$Null,"Home Directory Share",$Null,$securityDescriptor)
   
}
</pre>
</div>

Enjoy!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->