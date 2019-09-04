---
id: 1196
title: 'AD Script to Link GPO&#8217;s via the command line'
date: 2016-04-25T11:47:29-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/706-autosave-v1/
permalink: /2016/04/25/706-autosave-v1/
---
I&#8217;ve modified a script I found online to allow standard batch file passthrough for linking a GPO to a OU.

Usage: cscript.exe linkGPO.vbs &#8220;Test GPO&#8221; &#8220;lab.com&#8221; &#8220;OU=AD Project,DC=lab,DC=com&#8221;

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
elseif objGPOList.Count &gt; 1 then
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

if Err.Number &lt;&gt; 0 then
WScript.Echo "There was an error creating the GPO link."
WScript.Echo "Error: " & Err.Description
else
WScript.Echo "Sucessfully linked GPO to OU"
end if
</pre>

<span style="font-size: 78%;">Â </span>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->