---
id: 706
title: AD Script to Link GPOs via the command line
date: 2011-06-28T15:54:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/06/28/ad-script-to-link-gpos-via-the-command-line/
permalink: /2011/06/28/ad-script-to-link-gpos-via-the-command-line/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/06/ad-script-to-link-gpos-via-command-line.html
blogger_internal:
  - /feeds/7977773/posts/default/4947023596826803012
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - Group Policy
---
I've modified a script I found online to allow standard batch file passthrough for linking a GPO to a OU.
Usage: cscript.exe linkGPO.vbs "Test GPO" "lab.com" "OU=AD Project,DC=lab,DC=com"

<pre class="lang:vb decode:true ">If WScript.Arguments.Count = 3 Then
strGPO = WScript.Arguments.Item(0)
strDomain = WScript.Arguments.Item(1)
strOU = WScript.Arguments.Item(2)
Else
Wscript.Echo "Usage: linkGPO.vbs ""GPO Name"" ""Domain name"" OUs"
Wscript.Echo "Usage: linkGPO.vbs ""Test GPO"" ""lab.com"" ""OU=AD Project,DC=lab,DC=com"""
Wscript.Quit
End If


' This code links a GPO to an OU
' ------ SCRIPT CONFIGURATION ------
'strGPO = "zCCS IE 7" ' e.g. "Sales GPO"
'strDomain = "lab.com" ' e.g. "rallencorp.com"
'strOU = "OU=Offices,OU=AD Project,DC=lab,DC=com" ' e.g. "ou=Sales,dc=rallencorp,dc=com"
intLinkPos = -1 ' set this to the position the GPO evaluated at
' a value of -1 signifies appending it to the end of the list
' ------ END CONFIGURATION ---------

set objGPM = CreateObject("GPMgmt.GPM")
set objGPMConstants = objGPM.GetConstants( )

' Initialize the Domain object
set objGPMDomain = objGPM.GetDomain(strDomain, "", objGPMConstants.UseAnyDC)

' Find the specified GPO
set objGPMSearchCriteria = objGPM.CreateSearchCriteria
objGPMSearchCriteria.Add objGPMConstants.SearchPropertyGPODisplayName, _
objGPMConstants.SearchOpEquals, cstr(strGPO)
set objGPOList = objGPMDomain.SearchGPOs(objGPMSearchCriteria)
if objGPOList.Count = 0 then
WScript.Echo "Did not find GPO: " & strGPO
WScript.Echo "Exiting."
WScript.Quit
elseif objGPOList.Count > 1 then
WScript.Echo "Found more than one matching GPO. Count: " & _
objGPOList.Count
WScript.Echo "Exiting."
WScript.Quit
else
WScript.Echo "Found GPO: " & objGPOList.Item(1).DisplayName
end if

' Find the specified OU
set objSOM = objGPMDomain.GetSOM(strOU)
if IsNull(objSOM) then
WScript.Echo "Did not find OU: " & strOU
WScript.Echo "Exiting."
WScript.Quit
else
WScript.Echo "Found OU: " & objSOM.Name
end if

on error resume next

set objGPMLink = objSOM.CreateGPOLink( intLinkPos, objGPOList.Item(1) )

if Err.Number <> 0 then
WScript.Echo "There was an error creating the GPO link."
WScript.Echo "Error: " & Err.Description
else
WScript.Echo "Sucessfully linked GPO to OU"
end if
</pre>
