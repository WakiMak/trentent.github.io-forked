---
id: 634
title: Import-Csv -header $variable
date: 2013-07-17T11:42:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2013/07/17/import-csv-header-variable/
permalink: /2013/07/17/import-csv-header-variable/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2013/07/import-csv-header-variable.html
blogger_internal:
  - /feeds/7977773/posts/default/7972876777389819734
categories:
  - Blog
  - Uncategorized
tags:
  - PowerShell
  - scripting
---
I ran into an issue with Import-CSV where I was trying to pass a variable to the -header and it was failing pretty horribly, creating a single field instead of multiple fields.

I created a script to generate a CSV from Citrix WebInterface .conf files to compare the various web interfaces we have so that when we migrate users from the older interface to the newer interfaces we can properly communicate to them the changes they will experience.  In the course of developing this script I wanted to do a "dir *.conf" command and use that output as the header in the CSV.  Here is what I did originally:

<pre class="lang:ps decode:true ">$dirlist = Get-ChildItem -filter *.conf | Select-Object -ExpandProperty Name
#header value in powershell "Import-CSV" must be an array, a text variable parses as one header item
$header = "Value" 
foreach ($item in $dirlist) {
$header += $item 
}
#import csv with our header
$values = import-csv Values.txt  -Header $header</pre>

This failed.  Our $header variable became a single header in the CSV.  The help for "Import-CSV" says the -header should have a string value.  I tried $header.ToString() but it didn't work either.  I found you need to do the following:

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