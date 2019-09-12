---
id: 687
title: Purposeful limitations
date: 2012-02-15T15:54:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/02/15/purposeful-limitations/
permalink: /2012/02/15/purposeful-limitations/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/02/purposeful-limitations.html
blogger_internal:
  - /feeds/7977773/posts/default/114893643237682049
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerShell
  - scripting
  - VMWare
---
I have a Citrix environment without a provisioning server. This means that since I'm going to build my VM's out as opposed to up I need to script a method to automate deployment of Citrix servers within the VM environment. Fortunately, VMWare gives us the PowerCLI. A PowerShell that you can use to manipulate VMWare.

While I was working on this I ran into some some weird issues while working with VMWare and deploying OS Customizations.

It turns out if you burn your 3 activations VMWare cannot customize the OS anymore from VMWare. BUT(!) there is a dirty little trick that works for Server 2008 R2 (only one I've tested anyhow) that can allow you to work around the issue. The issue is VMWare's OS Customizations does a Generalize pass using Sysprep. If you exceed the 3 activations, sysprep will fail because it will notice this at the Generalize pass. The solution that I've read is to add this line to the sysprep.xml file:


```xml
<settings pass="generalize">
        <component name="Microsoft-Windows-Security-Licensing-SLC" processorArchitecture="x86" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <SkipRearm>1</SkipRearm>
        </component>
    </settings
```


http://support.microsoft.com/kb/929828

Unfortunately, VMWare does not appear to have a way to allow you to add the SkipRearm to the XML that it generates through the OS Customization GUI. But you can add a couple of registry keys that appear to have the same effect. They are:

```shell
REG ADD "HKEY_LOCAL_MACHINE\SYSTEM\Setup\Status\SysprepStatus" /v "GeneralizationState" /t REG_DWORD /d 0x7 /f"
REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform" /v "SkipRearm" /t REG_DWORD /d 0x1 /f
```


These two keys will signal to sysprep that this image can be generalized even though it has exceeded its activation count.

So, what I've found is you need to set these registry keys \*prior\* to shutting it down to convert into a template. Then when you deploy from template and it starts up and engages sysprep, sysprep will run without issue.

So how do you put this all together? Here is my scenario:  
We do NOT have a golden image template of our Citrix environment. This is because it is incredibly fluid. Changes occur to applications on the servers fairly regularly and documentation/memory is difficult to ensure that when we commit to putting these changes into the template that they are actually done. So how are we going to do this? We are going to take a 3 prong approach:  
1) We have a dev environment where developers can modify/change and generally mess up their VM's to their hearts content. The whole goal of this environment is to get their application working at 100%. The developers do not need to answer to anyone and have free reign to modify and experiment.  
2) We have a test environment where, when the developers think the application is tweaked/modified/configured exactly right; will pass off documentation to me to install in this environment. Any errors or modifications that happen outside of their documentation will be further documented and verified.  
3) Once step two is validated we can then push on to install on the production servers.

The issues we encounter is some of our application installs are huge, multi-step non-automated processes with large configuration tweaks post install. Once we get a solid install on one of the production servers our perferred method of redployment (because I've automated this process thus eliminating possible failure points) will be to template and redeploy with VMWare. This is the script I've written to accomplish this:

```powershell
function Clone-List{

Param(
[CmdletBinding()]
[Parameter(ValueFromPipeline=$true,
Position=0,
Mandatory=$true,
HelpMessage="Insert Message")]
[ValidateNotNullOrEmpty()]
$InputObject,
[Parameter(Position=1,
Mandatory=$false,
HelpMessage="Insert Preferred Folder")]
$folder,
[Parameter(Position=2,
Mandatory=$false,
HelpMessage="Insert Preferred Target Datastore")]
$datastore,
[Parameter(Position=3,
Mandatory=$false,
HelpMessage="Insert Preferred Target Host")]
$vmhost,
[Parameter(Position=4,
Mandatory=$false,
HelpMessage="Insert Preferred Disk Storage Format")]
[ValidateSet("Thick","Thin")]
$format = "Thin"
)


PROCESS{
$InputObject | %{
$vm = Get-VM $_
$name = $vm.name
$newname = -join("clone-",$name)
$templatename = -join("template-",$name)
If ($datastore -eq $null){
$datastore = Get-Datastore -VM $vm
}
If ($folder -eq $null){
$folder = $vm.Folder
}
If ($vmhost -eq $null){
$vmhost = Get-Cluster -VM $vm | Get-VMHost | Get-Random | Where{$_ -ne $null}
}
Write-Host "VM = $vm"
Write-Host "Name = $name"
Write-Host "NewName = $newname"
Write-Host "DataStore = $datastore"
Write-Host "Folder = $folder"
Write-Host "VMHost = $vmhost"

Write-Host "Setting up domain unjoin script..."
Remove-Item "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" 
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "netdom remove $name /Force"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "netdom renamecomputer $name /newname:XA6TEMPLATE /Force"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "REG ADD `"HKEY_LOCAL_MACHINE\SYSTEM\Setup\Status\SysprepStatus`" /v `"GeneralizationState`" /t REG_DWORD /d 0x7 /f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "REG ADD `"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform`" /v `"SkipRearm`" /t REG_DWORD /d 0x1 /f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "shutdown -s -t 20 -f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "del /q `"c:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd"
Write-Host "Setting Autologon..."
REG ADD "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "DefaultUserName" /d Administrator /f
REG ADD "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "DefaultPassword" /d 'password123' /f
REG ADD "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "AutoAdminLogon" /t REG_DWORD /d 0x5 /F
REG ADD "\\$name\HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "AutoLogonCount" /t REG_DWORD /d 0x1 /F

Write-Host "Preparing XenApp for Cloning..."
psexec \\$name "C:\Program Files (x86)\Citrix\&\ServerConfig\&ConfigConsole.exe" /ExecutionMode:ImagePrep /PrepMsmq:True

Write-Host "Cloning"
New-VM -Name $newname -VM $vm -Location $folder -Datastore $datastore -VMHost $vmhost -DiskStorageFormat $format
Write-Host "Unplugging NIC..." 
get-VM $newname | get-networkadapter | set-networkadapter -startconnected:$false -confirm:$false
Write-Host "Starting VM..."
start-vm $newname 

Write-Host "Powering on clone..."

REG ADD "\\$name\HKLM\SYSTEM\Setup\Status\SysprepStatus" /v "GeneralizationState" /t REG_DWORD /d 0x3 /f
Remove-Item "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" 
REG DELETE "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "DefaultUserName" /f
REG DELETE "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "DefaultPassword" /f
REG DELETE "\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "AutoAdminLogon" /f
REG DELETE "\\$name\HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v "AutoLogonCount" /f

Write-Host "We need to wait until the clone is powered off before we can template it..."

do {
sleep 1.0
Write "Waiting for shutdown of VMs"
} until (Get-VM $newname | Where { $_.PowerState -eq "poweredOff" })
write-host "System is powered on"

Write-Host "Creating NIC..." 
get-VM $newname | get-networkadapter | set-networkadapter -startconnected:$true -confirm:$false
Write-Host "Removing any stale template-VM's"
remove-template $templatename -confirm:$false
Write-Host "Creating Template VM from clone"
new-template -VM $newname -name $templatename -Location $folder
Write-Host "Removing Clone VM"
remove-vm $newname -DeletePermanently -confirm:$false


Write-Host "Setting up OS customization"
$cs = Get-OSCustomizationSpec PowerCLI
Set-OSCustomizationSpec -OSCustomizationSpec PowerCLI -ChangeSID:$true -Domain CCS.CORP -DomainUsername admin-ttye -DomainPassword password123 -NamingScheme Fixed -NamingPrefix CDCVXAP03P
new-vm -VMHost (Get-Cluster -VM $vm | Get-VMHost | Get-Random | Where{$_ -ne $null}) -name CDCVXAP03P -Template template-CDCVXAP02P -Datastore $datastore -OSCustomizationSpec $cs
get-VM CDCVXAP03P | get-networkadapter | set-networkadapter -startconnected:$true -confirm:$false

}
}
}
```

I've shamelessly stolen and modified this script from elsewhere. To execute this script, copy and paste it in a PowerGUI prompt then run:

> 
```powershell
get-vm CDCVXAP02P | Clone-Lis
```


It will execute the following:  
1) Pull the following parameters from the target VM to reclone:  
The VM Object  
The VM Objects Name  
The New Name for the temp clone  
A Template Name  
The Datastore the VM resides on  
The folder the VM resides in  
And the VM Host  
2) We then copy a script to our target machine that does the following:  
Removes the machine from the domain  
Renames the machine  
Prepares the addition of our two Sysprep registry key fixes  
Prepares a 20 second count down  
Deletes the script that executes step 2.  
3) We add the default username and password and autologon registry values. As of this writting this is not working for some reason  
4) We execute the command that preps the machine for cloning with Citrix. The machine you run this against will need to be rebooted as this will prevent new logons, but existing should continue (I think).  
5) We start the cloning process and unplug the NIC on the new machine. We don't want the new machine to come up and unjoin the original machine from the domain. From here, it will auto-power on. Ideally, it will login automatically and run the preconfigured script. (You may have to login manually to get it to do its thing) Once done it should shut itself down.  
6) Now VMWare will wait until the machine is powered off. Once it's powered off it will reconnect the NIC and make a template out of the VM and delete the temporary clone.  
7) Lastly it will now setup the OS Customization dynmically and create a VM with it.

Voila!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->