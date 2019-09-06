---
id: 683
title: Utilizing PowerShell to make Citrix VM Templates
date: 2012-03-05T14:59:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/03/05/utilizing-powershell-to-make-citrix-vm-templates-2/
permalink: /2012/03/05/utilizing-powershell-to-make-citrix-vm-templates-2/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/03/utilizing-powershell-to-make-citrix-vm.html
blogger_internal:
  - /feeds/7977773/posts/default/2157428455577605237
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerCLI
  - PowerShell
  - VMWare

---
Because my company doesn't utilize provisioining servers for deploy new Citrix & servers, I've had to come up with a couple of PowerShell scripts to make VMWare Templates that I can then deploy multiple & servers. You need VMWare PowerCLI to run this script. This is my script:

> <pre class="lang:ps decode:true ">function create-template{


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
$date = get-date -uformat "-%Y-%m-%d"
$templatename = -join("template-",$name,$date)
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
write-Host "templatename=$templatename"
sleep 4

Write-Host "Setting up domain unjoin script..."
Remove-Item "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" 
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "netdom remove $name /Force"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "netdom renamecomputer $name /newname:XA6TEMPLATE /Force"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "REG ADD `"HKEY_LOCAL_MACHINE\SYSTEM\Setup\Status\SysprepStatus`" /v `"GeneralizationState`" /t 

REG_DWORD /d 0x7 /f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "REG ADD `"HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion

\SoftwareProtectionPlatform`" /v `"SkipRearm`" /t REG_DWORD /d 0x1 /f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "`"C:\Program Files (x86)\Citrix\&\ServerConfig\&ConfigConsole.exe`" 

/ExecutionMode:ImagePrep /PrepMsmq:True"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "shutdown -s -t 90 -f"
Add-Content "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" "del /q `"c:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd"
Write-Host "Setting Autologon..."
psexec \\$name "REG.EXE" ADD `"\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`" /v `"DefaultUserName`" /d Administrator /f`"
sleep 5
psexec \\$name "REG.EXE" ADD `"\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`" /v `"DefaultPassword`" /d Hello /f`"
sleep 5
psexec \\$name "REG.EXE" ADD `"\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`" /v `"AutoAdminLogon`" /t REG_DWORD /d 0x1 /F`"
sleep 5
psexec \\$name "REG.EXE" ADD `"\\$name\HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`" /v `"AutoLogonCount`" /t REG_DWORD /d 0x1 /F`"

Write-Host "Cloning"
New-VM -Name $newname -VM $vm -Location $folder -Datastore $datastore -VMHost $vmhost -DiskStorageFormat $format
Write-Host "Unplugging NIC..." 
get-VM $newname | get-networkadapter | set-networkadapter -startconnected:$false -confirm:$false
Write-Host "Starting VM..."
start-vm $newname 

Write-Host "Powering on clone..."
Remove-Item "\\$name\c$\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\remove.cmd" 

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
}
}
}</pre>

This script does the following:  
1) Sets the inputs from a piped in object (get-vm VMTOTEMPLATE | create-template)  
2) Sets a series of variables ($vm, $name, $newname, $date, $templatename, etc.)  
3) We setup a startup script on the target server to make into a template that:  
a) Removes the computer from the domain  
b) renames the computer to a generic name (XATEMPLATE)  
c) Adds registry keys that will allow sysprep to run  
d) Configures & to "Image" mode  
e) Shuts itself down once running the script is complete  
f) deletes the script from running on startup  
4) We then set the target to autologin with the local admin user name and password so the startup script in step 3 will be run  
5) Begins the cloning by making a new-vm with the target machine  
6) We unplug the NIC from VMWare so that when it starts up the script won't actually remove the machine from the domain, but will remove itself from the domain  
7) start the clone  
8) the PowerCLI will now wait till the machine turns itself off...  
9) Then it will reconnect the NIC, remove any stale templates and then makes a new template and then removes the clone VM.

Done! 🙂

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->