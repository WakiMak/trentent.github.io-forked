---
id: 1095
title: Import-Csv -header $variable
date: 2016-04-25T07:55:03-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/634-revision-v1/
permalink: /2016/04/25/634-revision-v1/
---
I ran into an issue with Import-CSV where I was trying to pass a variable to the -header and it was failing pretty horribly, creating a single field instead of multiple fields.

I created a script to generate a CSV from Citrix WebInterface .conf files to compare the various web interfaces we have so that when we migrate users from the older interface to the newer interfaces we can properly communicate to them the changes they will experience. Â In the course of developing this script I wanted to do a &#8220;dir *.conf&#8221; command and use that output as the header in the CSV. Â Here is what I did originally:

<pre class="lang:ps decode:true ">$dirlist = Get-ChildItem -filter *.conf | Select-Object -ExpandProperty Name
#header value in powershell "Import-CSV" must be an array, a text variable parses as one header item
$header = "Value" 
foreach ($item in $dirlist) {
$header += $item 
}
#import csv with our header
$values = import-csv Values.txt  -Header $header</pre>

This failed. Â Our $header variable became a single header in the CSV. Â The help for &#8220;Import-CSV&#8221; says the -header should have a string value. Â I tried $header.ToString() but it didn&#8217;t work either. Â I found you need to do the following:

<pre class="lang:default decode:true ">$dirlist = Get-ChildItem -filter *.conf | Select-Object -ExpandProperty Name
#header value in powershell "Import-CSV" must be an array, a text variable parses as one header item
$header = @()
$header += "Value" 
foreach ($item in $dirlist) {
$header += $item 
}
#import csv with our header
$values = import-csv Values.txt  -Header $header</pre>

This makes an array then adds the appropriate values to the $header variable and the import-csv now has multiple columns.

Hurray!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->