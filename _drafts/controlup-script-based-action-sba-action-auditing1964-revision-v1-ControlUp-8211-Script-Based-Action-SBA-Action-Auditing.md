---
id: 1977
title: 'ControlUp &#8211; Script Based Action (SBA) Action Auditing'
date: 2017-01-27T08:57:46-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/01/27/1964-revision-v1/
permalink: /2017/01/27/1964-revision-v1/
---
[ControlUp](http://controlup.com) is a tool we use to monitor our Citrix environment. Â We have multiple people and multiple times actions are runÂ via ControlUp and an easier way to review the actions would be nice. Â ControlUp keeps all machine actions executed by them on the local machine&#8217;s event log. Â To review these logs I decided, what better way than to use ControlUp!

The Script Based Action (SBA):

<pre class="lang:ps decode:true">&lt;#
    .SYNOPSIS
    This script will return logging information about any ControlUp actions.

    .DESCRIPTION
    This script is a (minor) modification of David Falkus's original script for getting AppV events.  He documented everything that went into making
    this work here:  https://blogs.technet.microsoft.com/virtualshell/2016/08/25/app-v-5-troubleshooting-the-client-using-the-event-logs/

    This script takes two arguments.  
    $args[0] is a computer name
    $args[1] is GUI for a GUI or TEXT for text ouput.
    $args[2] is how far back in time to check for events (in hours).  No argument is all events.

    AUTHOR: Trentent Tye, David Falkus
    LASTEDIT: 01/26/2017
    VERSI0N : 1.0

#&gt;

# Adding threading culture change so that get-winevent picks up the messages, if PS culture is set to none en-US then the script will fail
[System.Threading.Thread]::CurrentThread.CurrentCulture = New-Object "System.Globalization.CultureInfo" "en-US"


if ($args[2]) {
#convert time into milliseconds
$time = [int]$args[2]*1000*60*60

$FilterXML = @"
&lt;QueryList&gt;
  &lt;Query Id="0" Path="Application"&gt;
    &lt;Select Path="Application"&gt;
    *[System[Provider[@Name='ControlUp action auditing'] 
    and TimeCreated[timediff(@SystemTime) &lt;= $($time)]]]
    &lt;/Select&gt;
  &lt;/Query&gt;
&lt;/QueryList&gt;
"@
} else {
$FilterXML = @"
&lt;QueryList&gt;
  &lt;Query Id="0" Path="Application"&gt;
    &lt;Select Path="Application"&gt;*[System[Provider[@Name='ControlUp action auditing']]]&lt;/Select&gt;
  &lt;/Query&gt;
&lt;/QueryList&gt;
"@
}

Try {
    $GWE_All = Get-WinEvent -FilterXml $FilterXML -ComputerName $args[0] -ErrorAction SilentlyContinue
} Catch {
    # capture any failure and display it in the error section, then end the script with a return
    # code of 1 so that CU sees that it was not successful.
    Write-Error "Unable to connect remotely to server to pull the event log" -ErrorAction Continue
    Write-Error $Error[1] -ErrorAction Continue
    Exit 1
}

#create a new object because previous events may not be defined if this is a non-persistent system
#the event contains all the data but without it defined on the server the message property maybe blank
$Events = @()

foreach ($event in $GWE_All) {
    [xml]$eventXML = $event.ToXML()
    $prop = New-Object System.Object
    $prop | Add-Member -type NoteProperty -name TimeCreated -value ([datetime]$eventXML.Event.System.TimeCreated.SystemTime)
    $prop | Add-Member -type NoteProperty -name Message -value $eventXML.Event.EventData.Data
    $Events += $prop
}

$Events = $Events | sort TimeCreated -Descending

#################
# Out-GridView
#################
if ($args[1] -eq "GUI") {
    $Events | select TimeCreated,Message | Out-GridView -Title $args[0] -Wait
} 
if ($args[1] -eq "TEXT") {
    $Events | select TimeCreated,Message |fl
}
</pre>

And the steps to create the SBA:

  1. Create a new SBA and name it &#8220;ControlUp Action Auditing&#8221; and click &#8216;Next&#8217;  
<img class="aligncenter size-full wp-image-1966" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA1.png" alt="" width="596" height="443" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA1.png 596w, http://theorypc.ca/wp-content/uploads/2017/01/SBA1-300x223.png 300w" sizes="(max-width: 596px) 100vw, 596px" /> 
  2. Set the &#8216;Assigned to:&#8217; &#8220;Computer&#8221; and &#8216;Execution Context:&#8217; as &#8220;ControlUp Console&#8221;  
<img class="aligncenter size-full wp-image-1967" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA2.png" alt="" width="596" height="445" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA2.png 596w, http://theorypc.ca/wp-content/uploads/2017/01/SBA2-300x224.png 300w" sizes="(max-width: 596px) 100vw, 596px" /> 
  3. Add the script and set the &#8216;Execution Timeout (seconds)&#8217; to whatever will satisfy querying your remote systems (I set mine to 120).  
<img class="aligncenter size-full wp-image-1968" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA3.png" alt="" width="598" height="446" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA3.png 598w, http://theorypc.ca/wp-content/uploads/2017/01/SBA3-300x224.png 300w" sizes="(max-width: 598px) 100vw, 598px" /> 
  4. Setup the variables  
<img class="aligncenter size-full wp-image-1969" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA6.png" alt="" width="592" height="150" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA6.png 592w, http://theorypc.ca/wp-content/uploads/2017/01/SBA6-300x76.png 300w" sizes="(max-width: 592px) 100vw, 592px" /> $args[0] = &#8216;Name&#8217; property  
    $args[1]:Â<img class="aligncenter size-full wp-image-1970" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA4.png" alt="" width="437" height="417" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA4.png 437w, http://theorypc.ca/wp-content/uploads/2017/01/SBA4-300x286.png 300w" sizes="(max-width: 437px) 100vw, 437px" />  
    (note the &#8220;pipe&#8221; symbol in the &#8216;Input Validation string&#8217;  
    $args[2]:Â<img class="aligncenter size-full wp-image-1971" src="http://theorypc.ca/wp-content/uploads/2017/01/SBA5.png" alt="" width="438" height="414" srcset="http://theorypc.ca/wp-content/uploads/2017/01/SBA5.png 438w, http://theorypc.ca/wp-content/uploads/2017/01/SBA5-300x284.png 300w" sizes="(max-width: 438px) 100vw, 438px" /> 
  5. Save and Finalize the SBA. Â And now it in action:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1977-64" width="1140" height="878" poster="http://theorypc.ca/wp-content/uploads/2017/01/ControlUpActionAuditing-mov-image.jpg" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/01/ControlUpActionAuditing.mp4?_=64" /><a href="http://theorypc.ca/wp-content/uploads/2017/01/ControlUpActionAuditing.mp4">http://theorypc.ca/wp-content/uploads/2017/01/ControlUpActionAuditing.mp4</a></video>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->