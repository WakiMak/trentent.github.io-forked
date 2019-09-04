---
id: 2979
title: Untitled
date: 2019-05-09T21:55:12-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2019/05/09/2977-revision-v1/
permalink: /2019/05/09/2977-revision-v1/
---
&nbsp;

<pre class="lang:default decode:true  ">Param (
    [Parameter(Position=0,mandatory=$true)]    [int] $SessionId,
    [Parameter(Position=1,mandatory=$true)][version] $CitrixClientVersion
)

#requires -version 3
$ErrorActionPreference = 'Stop'

&lt;#
    .SYNOPSIS
    Sends a message to users with an older version of Citrix Receiver/Workspace app

    .DESCRIPTION
    Sends a message to users with an older version of Citrix Receiver/Workspace app.  The "minimum" version is hardcoded in this script.

    .PARAMETER SessionId
    The Session Id of the user.

    .PARAMETER CitrixClientVersion
    A string with the version in the format XX.xx

    .EXAMPLE
    Send-Message -SessionId 3 -CitrixClientVersion 14.4.0

    .NOTES
    The preferred version is hardcoded into this script.
    The message itself is hardcoded in this script.  
    To change the message or version level target, you will need to edit this script.
    
#&gt;



[version]$minimumCitrixVersion = "19.0"
$message = @"
Hello, your Citrix Client is at version $CitrixClientVersion.

The minimum recommended version is $minimumCitrixVersion. 

Please upgrade your Citrix Client at: https://www.citrix.com/downloads/workspace-app/"
"@

Start-Process msg.exe -ArgumentList ("$SessionId","/TIME:0",$message)
</pre>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->