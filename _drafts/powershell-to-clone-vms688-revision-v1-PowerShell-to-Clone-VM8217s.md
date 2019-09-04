---
id: 1180
title: 'PowerShell to Clone VM&#8217;s'
date: 2016-04-25T11:20:29-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/688-revision-v1/
permalink: /2016/04/25/688-revision-v1/
---
I need to create a Provisioning Server of my own. We don&#8217;t want to purchase the software to do so and we may not need to do so&#8230; VMWare has PowerCLI which may provide enough to do the following:

1) Notify the VM that it needs to disable Citrix logins  
2) Have the VM disable logins and then check for logins. If none are found kick-off the cloning process. Kick off consists of:  
2a) Unjoin from the domain  
2b) Shutdown  
If users are still logged in, log them off forcefully at midnight and start the process  
3) Use VMWare Templates to clone the VM with the VM name.  
4) Shutdown original VM&#8217;s with the same names  
5) Startup new VM&#8217;s and join to domain&#8230;

Done?

I have a script that does the cloning&#8230; I got it from this site:  
http://www.vtesseract.com/post/16447807254/clone-list-powercli-function  
http://communities.vmware.com/docs/DOC-18155

I had issues with running it though. For some reason my PowerShell wouldn&#8217;t run it with the comments in it so I had to take them out:

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

I will need to modify this script to see if I can use VMWare Templates (I think that&#8217;s the right terminology) and Citrix XA PowerShell to see if I can get this to work&#8230; We shall see 🙂

EDIT &#8211; It&#8217;s not VMWare Templates&#8230; It&#8217;s OSCustomizationSpec I think.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->