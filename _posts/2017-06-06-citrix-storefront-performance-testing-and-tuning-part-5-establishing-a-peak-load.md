---
id: 2326
title: Citrix Storefront â€“ Performance Testing and Tuning â€“ Part 5 - Establishing a Peak Load
date: 2017-06-06T08:17:21-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2326
permalink: /2017/06/06/citrix-storefront-performance-testing-and-tuning-part-5-establishing-a-peak-load/
image: /wp-content/uploads/2017/05/rate_icon.png
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - Performance
  - PowerShell
  - Storefront
---
[In part 1 I setup a script](https://theorypc.ca/2017/05/24/citrix-storefront-performance-testing-and-tuning-part-1/) to load test our Citrix Storefront server, as a user logging in through the website. Â [Part 2 was users connecting](https://theorypc.ca/2017/05/30/citrix-storefront-performance-and-tuning-part-2-pna-traffic-simulation/) via PNA. Â In this part I'm going to examine our existingÂ load. Â In order to do so, I only want to count the following users:

  1. Users must have logged into Web Interface. Â This ensures that we count users who have actually logged in to launch an application. Â Some of our users may have the Web Interface set as their homepage so this way we can avoid idle user counts.
  2. Users who have launched an application. Â I'm interested in the users who have actually launched an application via Web Interface.
  3. PNA connections
  4. PNA launches

I want to get these user counts sorted via time in order to determine our peak load.

To get these counts I decided to key into some unique web URL's or resources. Â For #1, I decided to search the IIS logs for the "Logoff.png".

<img class="aligncenter size-full wp-image-2327" src="http://theorypc.ca/wp-content/uploads/2017/05/LogOffPng.png" alt="" width="419" height="117" srcset="http://theorypc.ca/wp-content/uploads/2017/05/LogOffPng.png 419w, http://theorypc.ca/wp-content/uploads/2017/05/LogOffPng-300x84.png 300w" sizes="(max-width: 419px) 100vw, 419px" /> 

This icon will only be pulled down for a user who has logged into the Web Interface.

For #2, I decided to look at the IIS logs and search for "launch.ica". Â This will get us the application launches via the sites.

<img class="aligncenter size-full wp-image-2328" src="http://theorypc.ca/wp-content/uploads/2017/05/Launchica_iislog.png" alt="" width="614" height="39" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Launchica_iislog.png 614w, http://theorypc.ca/wp-content/uploads/2017/05/Launchica_iislog-300x19.png 300w" sizes="(max-width: 614px) 100vw, 614px" /> 

For #3, I decided to look at the IIS logs and search for "config.xml". Â This file is pulled each time PNA is launched or a user logs onto the machine.

For #4, I decided to look at the IIS logs and search for "launch.aspx".

## 

## Users must have logged into Web Interface.

For #1 I created a powershell script to parse the IIS log file and export my data:

<pre class="lang:ps decode:true"># Define the location of log files and a temporary file
$LogFolder = "\\web01.bottheory.local\d$\inetpub\logs\LogFiles\W3SVC1"
$LogFiles = [System.IO.Directory]::GetFiles($LogFolder, "*.log")

#properties for stats to export to CSV

$obj = [System.Collections.ArrayList]::new()


function getElements ($date, $URI) {

$array = @()
$prop = New-Object System.Object
$prop | Add-Member -type NoteProperty -name "DateTime" -value $date
$prop | Add-Member -type NoteProperty -name "URI" -value $URI
$array = @($prop)
$obj.addrange($array)
}

foreach ($LogFile in $LogFiles) {
[Console]::WriteLine("$(Get-Date) $LogFile") #performance optimization from Microsoft
 try
 {
 $stream = [System.IO.StreamReader]::new($LogFile)
 while ($line = $stream.ReadLine())
 {
 if ($line[0] -eq "#") {continue}
 $data = $line.Split(" ")

 #write-host "0: $($data[0]) 1: $($data[1]) 2: $($data[2]) 3: $($data[3]) 4: $($data[4]) 5: $($data[5]) 6: $($data[6]) 7: $($data[7]) 8: $($data[8]) 9: $($data[9]) 10: $($data[10])"  ##uncomment "write-host" to see data
 
 #$data[4]
 if ($data[4] -like "*Logoff.png*") {
 $iisdateTime = "$($data[0]) $($data[1])"
 $iisdateTime = [datetime]$iisdateTime
 $iisdateTime = $iisdateTime.addHours(-7)  #fix up GMT-0 to my timezone
 getElements $iisdateTime $data[4]
 }

 #$data[4]
 if ($data[4] -like "*launch.ica*") {
 $iisdateTime = "$($data[0]) $($data[1])"
 $iisdateTime = [datetime]$iisdateTime
 $iisdateTime = $iisdateTime.addHours(-7) #fix up GMT-0 to my timezone
 getElements $iisdateTime $data[4]
 }
 #write-host $iisdateTime
 
 
 #pause
 }
 }
 finally
 {
 $stream.Dispose()
 }
}

$obj| export-csv WebInterface.csv</pre>

I then opened it in Excel and created time ranges per-hour and then counted how many of each item was found in that range. Â For the Logoff.PNG it was accessed like this:

<img class="aligncenter size-full wp-image-2355" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.57.41-PM.png" alt="" width="1333" height="591" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.57.41-PM.png 1333w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.57.41-PM-300x133.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.57.41-PM-768x341.png 768w" sizes="(max-width: 1333px) 100vw, 1333px" /> 

The peak count was 2775Â logons over a course of an hour. Â This equals ~45 logons per minute or 1 logon every 1.29 seconds.

In order to get even more precise, I broke down the hour that had the 2775 logons and looked at how many logons occurred over 15 minute time spans.

The peak rate over a 15 min span was:

##  <span style="color: #ff0000;">1 logon every 0.974Â second. (1.02 logons per second)</span>

* * *

## Users who have launched an application

<img class="aligncenter size-full wp-image-2356" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.59.08-PM.png" alt="" width="1330" height="590" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.59.08-PM.png 1330w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.59.08-PM-300x133.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.59.08-PM-768x341.png 768w" sizes="(max-width: 1330px) 100vw, 1330px" /> 

&nbsp;

For users who launch an application we are at 7593Â launches as a peak over an hour.

TheÂ peakÂ access to Web Interface is <span style="text-decoration: underline;">2.10Â launches per second</span>.

If we do a failover strategy to ensure , say in the future, we unified our architecture to a single location (or single cloud?) what would our combined rate be?

## <span style="color: #ff0000;"><strong>1Â launch every 0.561Â second. (1.78 launches per second)<br /> </strong></span>

Citrix has Storefront guidance that says it can handle connections/users in the ten's of thousands. Â I thought we were a large organization, but apparently Storefront has been designed for something even bigger! (Or web-based front ends are rarely the bottle neck...)

So these numbers areÂ _purely_ for logons through the web site. Â But previous testing for PNA functionality showed that it's a \*much\* heavier traffic to Web Interface. Â Why is the traffic heavier for PNA? Â Because each time a userÂ _logs on to a workstation_ it kicks in. Â Whereas accessing the web interface requires some work the user must do, PNA is automatic. Â It's also more convenient for users as they prefer to launch desktop/start menu icons.

* * *

## PNA connections

How am I going toÂ determine our concurrent connection rate for PNA? Â This is what a PNA connection looks like:

<img class="aligncenter size-full wp-image-2340" src="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront.png" alt="" width="638" height="103" srcset="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront.png 638w, http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront-300x48.png 300w" sizes="(max-width: 638px) 100vw, 638px" /> 

What I'm going to do is query the IIS logs for '<span style="text-decoration: underline;">config.xml</span>', as that file gets called only once when Receiver is launched or upon login. Â Once I get that I can search for 'enum.aspx' command that has been called and divide by the number of config.xml's. Â This should get me the number of icon requests per user.

After doing the mathÂ I found Receiver makesÂ **2.63** **enum.aspx** request for **every config.xml** requests.

Lastly,

My thought process here is once we get our peak 'Receiver' connection rate to the Storefront server we can create another script to simulate it. Â I know WCAT can do this as no funny cookie business occurs for Receiver.

So what does the PNA Receiver numbers look like?

We have a peak PNA connection rate of 7.531 config.xml connections per second. Â Multiplying by the additional 2.63 enum.aspx requests give us a total of **<span style="text-decoration: underline;">19.806 connections per second</span>** for a peak rate.

For PNA application launches, our peak rate is 30,075 launches in a single hour. Â This was measured by parsing for "launch.aspx". Â Breaking this hour down to 15 minute intervals and a single 15min window contained 53% of the launches. Â For our peak 15 minute window this is 16148 launches. Â This gives us a total of **17.94Â application** launches per second.

<table style="height: 129px;" width="747">
  <tr>
    <td width="74">
      Peak Rates
    </td>
    
    <td width="124">
      (in per second)
    </td>
    
    <td width="76">
    </td>
    
    <td width="104">
    </td>
    
    <td width="64">
    </td>
    
    <td width="64">
    </td>
  </tr>
  
  <tr>
    <td>
      PNA Logons
    </td>
    
    <td>
      PNA App Launches
    </td>
    
    <td>
      Web Logons
    </td>
    
    <td>
      Web LaunchICA
    </td>
    
    <td>
    </td>
    
    <td>
      Connections per second
    </td>
  </tr>
  
  <tr>
    <td>
      7.531
    </td>
    
    <td>
      17.94
    </td>
    
    <td>
      1.02
    </td>
    
    <td>
      1.78
    </td>
    
    <td>
    </td>
    
    <td>
      28.271
    </td>
  </tr>
</table>

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->