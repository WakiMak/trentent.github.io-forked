---
id: 1620
title: Using VMWare Remote Console with ControlUp
date: 2016-08-13T11:43:27-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1620
permalink: /2016/08/13/using-vmware-remote-console-with-controlup/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2016/08/VMRC2.mov
    179
    video/quicktime
    
categories:
  - Blog
tags:
  - ControlUp
  - PowerShell
  - scripting
  - VMWare
---
I wanted to connect to the console session of some of our VM&#8217;s but ControlUp doesn&#8217;t have a native way of doing so. Â Enter Script-Based-Actions and the ability to create those features! Â Here is a video of it in action:

[VMWare Remote Console on ControlUp](http://theorypc.ca/wp-content/uploads/2016/08/VMRC2.mov)

We use multiple individual vCenter servers so I have a list of them I need to connect to in order to find the VM and get the required data. Â This takes a bit longer but is still faster than running 6 different vCenter consoles. Â You will need to modify the vCenter list in my script and add your own:

<pre class="lang:ps decode:true ">&lt;#
    .SYNOPSIS
    This script will open a VMWare Remote Console on the selected server

    .DESCRIPTION
    This script requires VMWare's PowerCLI and VMRC installed on the same machine as the ControlUp Console.
    You need to modify the vCenter server list to your own.  This script will store the credentials you
    used to connect to the vCenter servers.  I've assumed you are using the same credentials for all
    vCenters, if you use different credentials for different servers you may need to set the credentials
    manually.  This script will go through and search each vcenter server for the VM and pull the required
    information needed to open a VMRC to it.

    PowerCLI can be downloaded here:
    http://blogs.vmware.com/PowerCLI/2016/03/new-release-powercli-6-3-r1download-today.html
    VMWare Remote Console can be downloaded here:
    https://my.vmware.com/web/vmware/details?downloadGroup=VMRC810&productId=491

    .LINK
    

<blockquote class="wp-embedded-content" data-secret="JxnLt4eqpl">
  <a href="http://theorypc.ca/">Home</a>
</blockquote>

    NAME: N/A
    AUTHOR: Trentent Tye, Theory PC
    LASTEDIT: 08/13/2016
    VERSI0N : 1.0
    WEBSITE: https://theorypc.ca

#&gt;

#Replace this list of vCenter servers with your own.
$vCenterServers = @(
"uxvcenter01.domain.local",
"wsvcenter20.domain.local",
"uxvcenter40.domain.local",
"wsvcenter40.domain.local",
"uxvcenter01.domain.local",
"wsvcenter01.domain.local"
)

If ( (Get-PSSnapin -Name VMware.VimAutomation.Core -ErrorAction SilentlyContinue) -eq $null )
{
    Try {
       #need to add the VMWare Modules Path to the ControlUp powershell modules environment otherwise we get a cis.core.service warning
       $env:PSModulePath = $env:PSModulePath + ";C:\Program Files (x86)\VMware\Infrastructure\vSphere PowerCLI\Modules"
        ipmo vmware.vimautomation.cis.core
        Add-PsSnapin VMware.VimAutomation.Core

    } Catch {
        # capture any failure and display it in the error section, then end the script with a return
        # code of 1 so that CU sees that it was not successful.
        Write-Error "Not able to load the SnapIn" -ErrorAction Continue
        Write-Error $Error[1] -ErrorAction Continue
        Exit 1
    }
}

if (-not(test-path $env:APPDATA\VMware\credstore\vicredentials.xml)) {
    #no credential file found, asking for credentials and we'll save them
    $VMWarecred = Get-Credential -Message "Enter your credentials for VMWare (DOMAIN\Username):"
    $VMWareUser = $VMWarecred.UserName.ToString()
    $VMWarePassPlainText = [Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($VMWareCred.Password))
        foreach ($vcenterServer in $vCenterServers) {
        New-VICredentialStoreItem -Host $vcenterServer -User $VMWareUser -Password $VMWarePassPlainText -File "$env:APPDATA\VMware\credstore\vicredentials.xml"
    }
}

Get-VICredentialStoreItem -file "$env:APPDATA\VMware\credstore\vicredentials.xml"
write-host "Connecting..."
Set-PowerCLIConfiguration -InvalidCertificateAction Ignore -confirm:$false
    foreach ($vcenterServer in $vCenterServers) {
    connect-viserver $vcenterServer
}


$VM = get-vm $args[0]

        foreach ($viserver in $global:defaultVIServers) {
          if (Get-VM -Name $VM.Name -Server $viserver -ErrorAction SilentlyContinue) {
          $vcenter = $viserver.serviceuri.Host
          $Session = Get-View -server $viserver -Id Sessionmanager
            }
        }
        $ticket =  $Session.AcquireCloneTicket()
        $vmid = ($vm).ExtensionData.moref.value

write-host "Launching VMRC"
Start-Process -FilePath "C:\Program Files (x86)\VMware\VMware Remote Console\vmrc.exe" -ArgumentList "vmrc://clone:$($ticket)@$($vcenter)/?moid=$($vmid)"
</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->