---
id: 1658
title: 'ControlUp &#8211; List AppV5 recent events on various servers'
date: 2016-08-26T15:15:13-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/08/26/1647-revision-v1/
permalink: /2016/08/26/1647-revision-v1/
---
[David Falkus just posted a blog post](https://blogs.technet.microsoft.com/virtualshell/2016/08/25/app-v-5-troubleshooting-the-client-using-the-event-logs/) on using Powershell to combine multiple AppV5 logs into a single view and orders them chronologically so you can see the events as they occurred.

Since this was a PowerShell script we can use ControlUp to import it, tweak it to accept some server variables and then get the output back to us. Â Here is a video of this in action:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1658-51" width="1140" height="773" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/08/ControlUp_AppV_Logs_Final.mp4?_=51" /><a href="http://theorypc.ca/wp-content/uploads/2016/08/ControlUp_AppV_Logs_Final.mp4">http://theorypc.ca/wp-content/uploads/2016/08/ControlUp_AppV_Logs_Final.mp4</a></video>
</div>

Here is the recipe for it:

<img class="aligncenter size-full wp-image-1654" src="http://theorypc.ca/wp-content/uploads/2016/08/1.png" alt="1" width="602" height="676" srcset="http://theorypc.ca/wp-content/uploads/2016/08/1.png 602w, http://theorypc.ca/wp-content/uploads/2016/08/1-267x300.png 267w" sizes="(max-width: 602px) 100vw, 602px" /> 

<img class="aligncenter size-full wp-image-1655" src="http://theorypc.ca/wp-content/uploads/2016/08/2.png" alt="2" width="603" height="680" srcset="http://theorypc.ca/wp-content/uploads/2016/08/2.png 603w, http://theorypc.ca/wp-content/uploads/2016/08/2-266x300.png 266w" sizes="(max-width: 603px) 100vw, 603px" /> 

<img class="aligncenter size-full wp-image-1656" src="http://theorypc.ca/wp-content/uploads/2016/08/3.png" alt="3" width="599" height="678" srcset="http://theorypc.ca/wp-content/uploads/2016/08/3.png 599w, http://theorypc.ca/wp-content/uploads/2016/08/3-265x300.png 265w" sizes="(max-width: 599px) 100vw, 599px" /> 

<img class="aligncenter size-full wp-image-1657" src="http://theorypc.ca/wp-content/uploads/2016/08/4.png" alt="4" width="601" height="678" srcset="http://theorypc.ca/wp-content/uploads/2016/08/4.png 601w, http://theorypc.ca/wp-content/uploads/2016/08/4-266x300.png 266w" sizes="(max-width: 601px) 100vw, 601px" /> 

&nbsp;

And the script:

<pre class="lang:ps decode:true  ">&lt;#
    .SYNOPSIS
    This script will return logging information amalgamating the AppV Admin, Operational and Virtual Applications logs.

    .DESCRIPTION
    This script is a (minor) modification of David Falkus's original script.  He documented everything that went into making
    this work here:  https://blogs.technet.microsoft.com/virtualshell/2016/08/25/app-v-5-troubleshooting-the-client-using-the-event-logs/


    AUTHOR: Trentent Tye, David Falkus
    LASTEDIT: 08/26/2016
    VERSI0N : 1.0

#&gt;

# Adding threading culture change so that get-winevent picks up the messages, if PS culture is set to none en-US then the script will fail
[System.Threading.Thread]::CurrentThread.CurrentCulture = New-Object "System.Globalization.CultureInfo" "en-US"

$FilterXML_Admin = @"
&lt;QueryList&gt;
  &lt;Query Id="0" Path="Microsoft-AppV-Client/Admin"&gt;
    &lt;Select Path="Microsoft-AppV-Client/Admin"&gt;*[System[TimeCreated[timediff(@SystemTime) &lt;= 86400000]]]&lt;/Select&gt;
  &lt;/Query&gt;
&lt;/QueryList&gt;
"@

Try {
    $GWE_All = Get-WinEvent -FilterXml $FilterXML_Admin -ComputerName $args[0] -ErrorAction SilentlyContinue
} Catch {
    # capture any failure and display it in the error section, then end the script with a return
    # code of 1 so that CU sees that it was not successful.
    Write-Error "Unable to connect remotely to server to pull the event log" -ErrorAction Continue
    Write-Error $Error[1] -ErrorAction Continue
    Exit 1
}

$FilterXML_Operational = @"
&lt;QueryList&gt;
  &lt;Query Id="0" Path="Microsoft-AppV-Client/Operational"&gt;
    &lt;Select Path="Microsoft-AppV-Client/Operational"&gt;*[System[TimeCreated[timediff(@SystemTime) &lt;= 86400000]]]&lt;/Select&gt;
    &lt;Suppress Path="Microsoft-AppV-Client/Operational"&gt;*[System[(EventID=101 or EventID=102 or EventID=14023 or EventID=14024 or EventID=14025 or EventID=14026)]]&lt;/Suppress&gt;
  &lt;/Query&gt;
&lt;/QueryList&gt;
"@

Try {
    $GWE_All += Get-WinEvent -FilterXml $FilterXML_Operational  -ComputerName $args[0] -ErrorAction SilentlyContinue
} Catch {
    # capture any failure and display it in the error section, then end the script with a return
    # code of 1 so that CU sees that it was not successful.
    Write-Error "Unable to connect remotely to server to pull the event log" -ErrorAction Continue
    Write-Error $Error[1] -ErrorAction Continue
    Exit 1
}

$FilterXML_VirtApps = @"
&lt;QueryList&gt;
  &lt;Query Id="0" Path="Microsoft-AppV-Client/Virtual Applications"&gt;
    &lt;Select Path="Microsoft-AppV-Client/Virtual Applications"&gt;*[System[TimeCreated[timediff(@SystemTime) &lt;= 86400000]]]&lt;/Select&gt;
  &lt;/Query&gt;
&lt;/QueryList&gt;
"@

Try {
    $GWE_All += Get-WinEvent -FilterXml $FilterXML_VirtApps  -ComputerName $args[0] -ErrorAction SilentlyContinue
} Catch {
    # capture any failure and display it in the error section, then end the script with a return
    # code of 1 so that CU sees that it was not successful.
    Write-Error "Unable to connect remotely to server to pull the event log" -ErrorAction Continue
    Write-Error $Error[1] -ErrorAction Continue
    Exit 1
}

$GWE_All = $GWE_All | sort TimeCreated -Descending

#################
# Out-GridView
#################

$GWE_All | select TimeCreated,Id,LogName,TaskDisplayName,LevelDisplayName,Message | Out-GridView -Title $args[0] -Wait
</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->