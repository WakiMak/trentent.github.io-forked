---
id: 688
title: PowerShell to Clone VM's
date: 2012-01-31T17:25:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2012/01/31/powershell-to-clone-vms/
permalink: /2012/01/31/powershell-to-clone-vms/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/01/powershell-to-clone-vms.html
blogger_internal:
  - /feeds/7977773/posts/default/2440959434536855583
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - PowerShell
  - scripting
  - VMWare
---
I need to create a Provisioning Server of my own. We don't want to purchase the software to do so and we may not need to do so... VMWare has PowerCLI which may provide enough to do the following:

1) Notify the VM that it needs to disable Citrix logins  
2) Have the VM disable logins and then check for logins. If none are found kick-off the cloning process. Kick off consists of:  
2a) Unjoin from the domain  
2b) Shutdown  
If users are still logged in, log them off forcefully at midnight and start the process  
3) Use VMWare Templates to clone the VM with the VM name.  
4) Shutdown original VM's with the same names  
5) Startup new VM's and join to domain...

Done?

I have a script that does the cloning... I got it from this site:  
http://www.vtesseract.com/post/16447807254/clone-list-powercli-function  
http://communities.vmware.com/docs/DOC-18155

I had issues with running it though. For some reason my PowerShell wouldn't run it with the comments in it so I had to take them out:

> <pre class="lang:ps decode:true ">function Clone-List{


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
If ($datastore -eq $null){
$datastore = Get-Datastore -VM $vm
}
If ($folder -eq $null){
$folder = $vm.Folder
}
If ($vmhost -eq $null){
$vmhost = Get-Cluster -VM $vm | Get-VMHost | Get-Random | Where{$_ -ne $null}
}
New-VM -Name $newname -VM $vm -Location $folder -Datastore $datastore -VMHost $vmhost -DiskStorageFormat $format -RunAsync
}
}
}</pre>

You can run it with a command like so:

> Get-VM MyVM | Clone-List

I will need to modify this script to see if I can use VMWare Templates (I think that's the right terminology) and Citrix XA PowerShell to see if I can get this to work... We shall see 

EDIT - It's not VMWare Templates... It's OSCustomizationSpec I think.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->