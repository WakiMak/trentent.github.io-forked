---
id: 2788
title: 'Citrix Provisioning Service &#8211; Network Service Starting/Stopping services remotely'
date: 2018-05-02T09:53:22-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2788
permalink: /2018/05/02/citrix-provisioning-service-network-service-starting-stopping-services-remotely/
image: /wp-content/uploads/2018/05/Status_Success.png
categories:
  - Blog
tags:
  - 2012R2
  - Citrix
  - PowerShell
  - Provisioning Services
  - PVS
  - scripting
---
Citrix Provisioning Services has a feature within the &#8220;Provisioning Services Console&#8221; that allows you to stop/restart/start the streaming service on another server:

<img class="aligncenter size-full wp-image-2789" src="http://theorypc.ca/wp-content/uploads/2018/05/Stop_Restart.png" alt="" width="387" height="352" srcset="http://theorypc.ca/wp-content/uploads/2018/05/Stop_Restart.png 387w, http://theorypc.ca/wp-content/uploads/2018/05/Stop_Restart-300x273.png 300w" sizes="(max-width: 387px) 100vw, 387px" /> 

&nbsp;

This feature worked with Server 2008R2 but with 2012R2 and greater it stopped working.Â [CitrixÂ _partially_ identified the issue here](https://docs.citrix.com/en-us/provisioning/7-15/managing-servers/server-services-start-stop.html):

<img class="aligncenter size-full wp-image-2790" src="http://theorypc.ca/wp-content/uploads/2018/05/importantConsiderations.png" alt="" width="735" height="687" srcset="http://theorypc.ca/wp-content/uploads/2018/05/importantConsiderations.png 735w, http://theorypc.ca/wp-content/uploads/2018/05/importantConsiderations-300x280.png 300w" sizes="(max-width: 735px) 100vw, 735px" /> 

&nbsp;

I was exploring starting and stopping the streaming service on other PVS servers from the Console and I found this information was incorrect.Â Adding the NetworkService doesÂ **NOT** enable the streaming service to be stop/started/restarted from other machines.Â The reason is the NETWORKSERVICE is a LOCAL account on the machine itself.Â When it attempts to reach out and communicate with another system it is translated into a proper SID, which matches the machine account.Â Since that SID communicating across the wire does not have access to the service you get a failure.

<img class="aligncenter size-full wp-image-2792" src="http://theorypc.ca/wp-content/uploads/2018/05/Services_Failed.png" alt="" width="442" height="357" srcset="http://theorypc.ca/wp-content/uploads/2018/05/Services_Failed.png 442w, http://theorypc.ca/wp-content/uploads/2018/05/Services_Failed-300x242.png 300w" sizes="(max-width: 442px) 100vw, 442px" /> 

In order to fix thisÂ _**properly**__Â_ we can add either the machine account permissions for each PVS Server on each service OR we can add all machine accounts into a security group and addÂ _**that**__Â_ as permissions to manipulate the service on each PVS Server.

I created a PowerShell script to enable easily add a group, user or machine account to the Streaming Service.Â It will also list all the permissions:

<img class="aligncenter size-full wp-image-2793" src="http://theorypc.ca/wp-content/uploads/2018/05/GetStreamServiceACL.png" alt="" width="841" height="726" srcset="http://theorypc.ca/wp-content/uploads/2018/05/GetStreamServiceACL.png 841w, http://theorypc.ca/wp-content/uploads/2018/05/GetStreamServiceACL-300x259.png 300w, http://theorypc.ca/wp-content/uploads/2018/05/GetStreamServiceACL-768x663.png 768w" sizes="(max-width: 841px) 100vw, 841px" /> 

An example adding a Group to the permissions to the service:

<img class="aligncenter size-full wp-image-2794" src="http://theorypc.ca/wp-content/uploads/2018/05/AddACEToService.png" alt="" width="1299" height="915" srcset="http://theorypc.ca/wp-content/uploads/2018/05/AddACEToService.png 1299w, http://theorypc.ca/wp-content/uploads/2018/05/AddACEToService-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2018/05/AddACEToService-768x541.png 768w" sizes="(max-width: 1299px) 100vw, 1299px" /> 

And now we can start the service remotely:

<img class="aligncenter size-full wp-image-2795" src="http://theorypc.ca/wp-content/uploads/2018/05/Services_Started.png" alt="" width="441" height="357" srcset="http://theorypc.ca/wp-content/uploads/2018/05/Services_Started.png 441w, http://theorypc.ca/wp-content/uploads/2018/05/Services_Started-300x243.png 300w" sizes="(max-width: 441px) 100vw, 441px" /> 

&nbsp;

In order to get this working entirely I recommend the following steps:

  1. Create a Group (eg, &#8220;CTX.Servers.ProvisioningServiceServer&#8221;)
  2. Add all the PVS Machine Accounts into that group
  3. Reboot your PVS server to gain that group membership token
  4. Run the powershell script on each machine to add the group permission to the streaming service: <pre class="lang:ps decode:true">. .\Add_Permissions_ToStreamService.ps1 -SetACL -Domain Bottheory -GroupOrUser CTX.Servers.ProvisioningServiceServer</pre>

  5. Done!

And now the script:

<pre class="lang:ps decode:true ">&lt;#
    .SYNOPSIS
      Adds a user or group to the permission set on the Citrix Streaming Service to enable remote service manipulation

    .DESCRIPTION
      Adds a user or group to the permission set on the Citrix Streaming Service to enable remote service manipulation.  This enables
      more precisely targeted permissions and allows security groups with the machine accounts of the PVS servers as members to
      remotely manipulate the streaming service.  Previously, a service account would be required to be set on the streaming service,
      not anymore!

    .PARAMETER GetACL
        Gets the Access Control List of the Streaming Service

    .PARAMETER SetACL
        Sets the mode to configuring the Streaming Service ACL

    .PARAMETER GroupOrUser
        The friendly name of a Group, User or Machine account

    .PARAMETER Domain
        The name of the active directory domain

    .EXAMPLE
        .\Add_Permissions_ToStreamService.ps1 -GetACL

    .EXAMPLE
        .\Add_Permissions_ToStreamService.ps1 -SetACL -GroupOrUser trententtye -Domain BOTTHEORY
            
    .INPUTS
        None

    .OUTPUTS
        None

    .NOTES

      Author: Trentent Tye
      Editor: Trentent Tye
      Company: TheoryPC

      History
      Last Change: 2018.05.01 TT: Script created

	.LINK
        

<blockquote class="wp-embedded-content" data-secret="BpbrGeCpiU">
  <a href="http://theorypc.ca/">Home</a>
</blockquote>
    #&gt;

## help from here: https://rohnspowershellblog.wordpress.com/2013/03/19/viewing-service-acls/

param(
  [parameter(Mandatory=$false)][switch]$GetACL,
  [Parameter(Mandatory=$false)][switch]$SetACL,      
  [Parameter(Mandatory=$false)][string]$Domain,
  [Parameter(Mandatory=$false)][string]$GroupOrUser
)

#ErrorAction Preferences
$ErrorActionPreference = "Stop"

Add-Type  @"
  [System.FlagsAttribute]
  public enum ServiceAccessFlags : uint
  {
      QueryConfig = 1,
      ChangeConfig = 2,
      QueryStatus = 4,
      EnumerateDependents = 8,
      Start = 16,
      Stop = 32,
      PauseContinue = 64,
      Interrogate = 128,
      UserDefinedControl = 256,
      Delete = 65536,
      ReadControl = 131072,
      WriteDac = 262144,
      WriteOwner = 524288,
      Synchronize = 1048576,
      AccessSystemSecurity = 16777216,
      GenericAll = 268435456,
      GenericExecute = 536870912,
      GenericWrite = 1073741824,
      GenericRead = 2147483648
  }
"@

#Get ACL of the streaming service and present it

function Get-StreamServiceACL {
    Write-Host "Listing permissions for StreamService: `r`n" -ForegroundColor Yellow
    $Sddl = sc.exe sdshow StreamService | where { $_ }
    $sd = New-Object System.Security.AccessControl.RawSecurityDescriptor ($Sddl)
    foreach ($acl in $sd.DiscretionaryAcl) {
        Write-Host "BinaryLength       : $($acl.BinaryLength)"
        Write-Host "AceQualifier       : $($acl.AceQualifier)"
        Write-Host "IsCallback         : $($acl.IsCallback)"
        Write-Host "OpaqueLength       : $($acl.OpaqueLength)"

        try {
            Write-Host "AccessMask         : $([ServiceAccessFlags]$acl.AccessMask)" #bitwise operation
        } catch {
            Write-Host "AccessMask         : $($acl.AccessMask)"
        }      
        try {
            $objSID = New-Object System.Security.Principal.SecurityIdentifier ($acl.SecurityIdentifier.Value)
            $objUser = $objSID.Translate( [System.Security.Principal.NTAccount])
            Write-Host "SecurityIdentifier : $($objUser.Value)"
        } catch {
            Write-Host "SecurityIdentifier : $($acl.SecurityIdentifier)"
        }        
        Write-Host "AceType            : $($acl.AceType)"
        Write-Host "AceFlags           : $($acl.AceFlags)"
        Write-Host "IsInherited        : $($acl.IsInherited)"
        Write-Host "InheritanceFlags   : $($acl.InheritanceFlags)"
        Write-Host "PropagationFlags   : $($acl.PropagationFlags)"
        Write-Host "AuditFlags         : $($acl.AuditFlags)"
        Write-Host "`r`n"
    }
    break
}

if ($getACL -or ($PSBoundParameters.Count -eq 0)) {
    Get-StreamServiceACL
}

if ($SetACL) {
    if (($GroupOrUser) -and ($Domain)) {
        Write-Host "Adding ACE for $Domain\$GroupOrUser" -ForegroundColor Yellow
        $aceQualifer = [System.Security.AccessControl.AceQualifier]::AccessAllowed
        $aceType = [System.Security.AccessControl.AceType]::AccessAllowed
        $aceFlags = [System.Security.AccessControl.AceFlags]::None
        $InheritanceFlags = [System.Security.AccessControl.InheritanceFlags]::None
        $PropagationFlags = [System.Security.AccessControl.PropagationFlags]::None
        $AuditFlags = [System.Security.AccessControl.AuditFlags]::None

        $objGroup = New-Object System.Security.Principal.NTAccount("$Domain", "$GroupOrUser")
        $strSID = $objGroup.Translate([System.Security.Principal.SecurityIdentifier])

        $accessMask = [int]60   # QueryStatus, EnumerateDependents, Start, Stop

        #need to add ACE to service
        $systemCommonAce = [System.Security.AccessControl.CommonAce]::new($aceFlags, $aceQualifer, $accessMask, $($strSid.Value), $false, $null)

        $Sddl = sc.exe sdshow StreamService | where { $_ }
        $sd = New-Object System.Security.AccessControl.RawSecurityDescriptor ($Sddl)
        $serviceCount = $sd.DiscretionaryAcl.Count
        $sd.DiscretionaryAcl.InsertAce($serviceCount, $systemCommonAce)
        sc.exe sdset streamservice $sd.GetSddlForm(3)
        Get-StreamServiceACL
    }

}</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->