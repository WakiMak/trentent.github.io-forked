---
id: 666
title: Modify XML files with PowerShell (for manipulating AppV)
date: 2013-01-08T16:28:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/01/08/modify-xml-files-with-powershell-for-manipulating-appv/
permalink: /2013/01/08/modify-xml-files-with-powershell-for-manipulating-appv/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/01/modify-xml-files-with-powershell-for.html
blogger_internal:
  - /feeds/7977773/posts/default/1622495563601388817
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
  - PowerShell
  - scripting
---
<div style="font-family: 'Courier New',Courier,monospace;">
  <pre class="lang:ps decode:true ">#===================================CHANGE OS VALUE=========================================== 
# 
#   By Trentent Tye 2012-12-19 
# 
#   This script will find all the applications in TEST and PROD and query the user to 
#   which application they want to change the OS VALUE in the OSD file.  You will be given 
#   four options.  1 - Remove any OS VALUE from the file, 2 - Set an OS VALUE, 
#    4 - Get current value (if exists) 
# 
# 
# 
# 
# 
# 

#===================================================================================== 
#===================================================================================== 

#setting up variables 
$APPV_Packages_Path = "\\jeeves\appv_images" 

#Which environment to change? 
$caption = "Choose Action"; 
$message = "Modify OS VALUE for which type of application?"; 
$Prod = new-Object System.Management.Automation.Host.ChoiceDescription "&Prod","Prod"; 
$Test = new-Object System.Management.Automation.Host.ChoiceDescription "&Test","Test"; 
$choices = [System.Management.Automation.Host.ChoiceDescription[]]($Test,$Prod); 
$answer = $host.ui.PromptForChoice($caption,$message,$choices,0) 
  
switch ($answer){ 
    0 {"You entered Test"; $Env = "TEST"} 
    1 {"You entered Prod"; $Env = "PROD"} 
} 

#===================================================================================== 
#===================================================================================== 

#get list of applications (osd files) and put into a dictionary and 
#display the key-value combos for user to enter a number 
#if the user does not enter a correct choice, show the screen again. 
Clear-Host 
$completedsuccessfully = $TRUE 
do { 
$list = dir $APPV_Packages_Path\$ENV -include *.osd -recurse | Select-Object -ExpandProperty Name  | sort 
$n = 0 

#create a dictionary and put all the OSD files into it and add a numerical value to each one 
$dict = New-Object 'System.Collections.Generic.Dictionary[String,String]' 

foreach ($i in $list) {  
$n++ 
$dict.Add("$n","$i") 
} 

#format the dictionary nicely... 
$dict | ft -auto 

$appChoice = read-host -Prompt "Please select an application" 
$appName = $dict.Get_Item("$appChoice") 
if ($?) { 
   $completedsuccessfully = $TRUE 
   } else { 
   Clear-Host 
   Write-Host "An invalid choice (" -nonewline; Write-Host "$appChoice" -f Red -nonewline; Write-Host ") was selected"; 
   $completedsuccessfully = $FALSE 
   } 
} until ($completedsuccessfully) 
Write-Host "You selected '" -nonewline; Write-Host "$appName" -f Yellow -nonewline; Write-Host "'"; 

#===================================================================================== 
#===================================================================================== 
#Setup functions for the next section 

function GetOSValue() { 
    #get the path to the OSD file 
    #Get OSD file as an XML 
    $OSDFile = dir $APPV_Packages_Path\$ENV -include $appName -recurse 
    [xml]$OSD = Get-Content "$OSDFile" 
    Write-host "======================================================================" 
    Write-host "" 
    Write-host "Application: " -nonewline; write-host "$appName" -f Yellow; 
    $OSD.SOFTPKG.IMPLEMENTATION.OS | fl | Out-String 
    Write-host "======================================================================" 
} 

function RemoveOSValue() { 
    #get the path to the OSD file 
    #Get OSD file as an XML 
    $OSDFile = dir $APPV_Packages_Path\$ENV -include $appName -recurse 
    [xml]$OSD = Get-Content "$OSDFile" 
    Write-host "======================================================================" 
    Write-host "" 
    Write-host "Application: " -nonewline; write-host "$appName" -f Yellow; 
    Write-host "" ; write-host "Original value:" 
    $OSD.SOFTPKG.IMPLEMENTATION.OS | fl 
    Write-Host "Removing ALL OS Value(s)..." 

    #select OS node from the XML file   
    #remove the node, continue until no more OS Values exist 
    try { 
        do { 
            $node = $OSD.SelectSingleNode("//SOFTPKG/IMPLEMENTATION/OS") 
            [Void]$node.ParentNode.RemoveChild($node) 
        } until ( $? -eq $FALSE ) 
    } catch { 
        write-host "Complete" 
        write-host "" 
    } 



    #backup the original file 
    $File = dir $APPV_Packages_Path\$ENV -include $appName -recurse 
    $fileObj = get-item $File 

    # Get the date 
    $DateStamp = get-date -uformat "%Y-%m-%d@%H-%M-%S" 

    #make our backup 
    copy "$File" "$File-$DateStamp" 

    # Display the new name 
    "Backup filename: $File-$DateStamp" 

    #save our file  
    $osd.Save($OSDfile) 
     

    Write-host "======================================================================" 
} 

function SetOSValue() { 
    #get the path to the OSD file 
    #Get OSD file as an XML 
    $OSDFile = dir $APPV_Packages_Path\$ENV -include $appName -recurse 
    [xml]$OSD = Get-Content "$OSDFile" 
    Write-host "======================================================================" 
    Write-host "" 
    Write-host "Application: " -nonewline; write-host "$appName" -f Yellow; 
    write-host "" 
    Write-Host "Setting OS Value..." 
    #select OS node from the XML file   
    # Creation of a nod ans his text 
    $xmlElt = $OSD.CreateElement("OS") 
    # Creation of an attribute in the principal nod 
    $xmlAtt = $OSD.CreateAttribute("VALUE") 

    #ask which OS you'd like 
    $caption = "Choose Action"; 
    $message = "Which OS do you want to deploy this app?"; 
    $2003 = new-Object System.Management.Automation.Host.ChoiceDescription "&3 - 2003 R2 x32","2003 R2 x32"; 
    $2008 = new-Object System.Management.Automation.Host.ChoiceDescription "&8 - 2008 R2 x64","2008 R2 x64"; 
    $choices = [System.Management.Automation.Host.ChoiceDescription[]]($2003,$2008); 
    $answer = $host.ui.PromptForChoice($caption,$message,$choices,0) 
  
    switch ($answer){ 
        0 {$xmlAtt.Value = "Win2003TS"} 
        1 {$xmlAtt.Value = "Win2008R2TS64"} 
    } 

     
    # Appending the attribute to the element 
    $xmlElt.Attributes.Append($xmlAtt) 
    # Appending the new element to the proper location 
    [void]$OSD.SOFTPKG.IMPLEMENTATION.AppendChild($xmlElt) 


    $osd.Save($OSDfile) 
     

    Write-host "======================================================================" 
} 


#===================================================================================== 
#===================================================================================== 

#What would they like to do to the file? 
function action() { 
$caption = "Choose Action"; 
$message = "Modify OS VALUE for which type of application?"; 
$1 = new-Object System.Management.Automation.Host.ChoiceDescription "&1 Remove OS VALUE","Remove OS VALUE"; 
$2 = new-Object System.Management.Automation.Host.ChoiceDescription "&2 Set an OS VALUE","Set an OS VALUE"; 
$3 = new-Object System.Management.Automation.Host.ChoiceDescription "&3",""; 
$4 = new-Object System.Management.Automation.Host.ChoiceDescription "&4 Get current OS VALUE","Get current OS VALUE"; 
$choices = [System.Management.Automation.Host.ChoiceDescription[]]($1,$2,$3,$4); 
$answer = $host.ui.PromptForChoice($caption,$message,$choices,3) 
  
switch ($answer){ 
    0 {RemoveOSValue;exit} 
    1 {RemoveOSValue;SetOSValue;exit} 
    2 {Write-Host "Incorrect Option, please try again"} 
    3 {GetOSValue} 
  } 
} 

#loop forever 
$loop = 1 
do {action} until ($loop = 0)</pre>
  
  <p>
    &nbsp;
  </p>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->