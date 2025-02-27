---
id: 700
title: Generate a CSV from your GPO's per OU
date: 2011-07-28T09:08:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2011/07/28/generate-a-csv-from-your-gpos-per-ou/
permalink: /2011/07/28/generate-a-csv-from-your-gpos-per-ou/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2011/07/generate-csv-from-your-gpos-per-ou.html
blogger_internal:
  - /feeds/7977773/posts/default/1747858035413338937
categories:
  - Blog
  - Uncategorized
tags:
  - Active Directory
  - Group Policy
  - scripting
---
If you come into an environment like I have, you'll find that some companies prefer to break out their AD structure by location and then generate a OU structure that matches it. Physically, this is understandable and you can understand what's where. Logically, this causes issues because AD utilizes an inheritance model and this gets complicated and very messy very quickly if you do not follow a strict model. This model falls on its face when you have a centralized IT force. As an example, the company I worked for acquired numerous other companies and a each company/location had it's own IT workforce. Eventually, the company consolidated all of these external IT departments into one. The IT staff then standardized each site for GPO's. Which made having each one redundant.

This is a mockup of the OU structure:  
[<img id="BLOGGER_PHOTO_ID_5634422303142100354" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 259px; height: 400px;" src="http://1.bp.blogspot.com/-W0a4Qbvmb6U/TjF9O3VSvYI/AAAAAAAAAG0/VCT-QRAfjxQ/s400/OU-Structure.GIF" alt="" border="0" />](http://1.bp.blogspot.com/-W0a4Qbvmb6U/TjF9O3VSvYI/AAAAAAAAAG0/VCT-QRAfjxQ/s1600/OU-Structure.GIF)

And the GPO's applied:  
[<img id="BLOGGER_PHOTO_ID_5634422887717899234" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 257px; height: 400px;" src="http://4.bp.blogspot.com/-KT8IXaKWGOI/TjF9w5DBi-I/AAAAAAAAAG8/P5fBuRaxSFE/s400/OU-w-GPO.GIF" alt="" border="0" />](http://4.bp.blogspot.com/-KT8IXaKWGOI/TjF9w5DBi-I/AAAAAAAAAG8/P5fBuRaxSFE/s1600/OU-w-GPO.GIF)

If you look closely, you can see that some sites are missing some GPO's, some have an extra GPO, and some have the same. The goal I was given is that I need to consolidate the OU's with the same GPO's applied and then I can examine the disparate ones individually. In order to make a nice spreadsheet to do this I created this script (run on Windows, I added awk, sed, and grep to the windowssystem32 folder and installed group policy management).

Since the structure has a nice, predictable "end" OU (eg, Laptops, Desktops, Users) I could script for that keyword:

```powershell
:Lets grab all the OU's
cscript "%programfiles%\gpmc\scripts\dumpsominfo.wsf" "Desktops" /showinheritedlinks > desktop-gpo-links.txt

:now we're going to parse out all the GPO's
:what this next line does is print the file to stdout, piping stdout to awk
:awk then grabs and prints out the text in the range "OU=Desktop" to "-- Who"
:this stdout is then piped to grep which retrieves the path from that selection of text
:grep then removes all other lines that contain path or two "--". From here,
:awk then removes the first column and prints out the rest of the GPO's. The last
:awk command then removes all duplicates.

type desktop-gpo-links.txt | awk "/OU=Desktop/,/-- Who/" | grep -v -E "Path" | grep -v -E "\-\-" | awk "{ for (i=2; i<=NF; i++) printf \"%%s \", $i; printf \"\n\"; }" | awk "! a[$0]++" | sed.exe "s/ $//g" > GPOs.txt

::::::::::
:Next we need to pull out all the OU's that this is applying against
type desktop-gpo-links.txt | grep -E "Path" > ou.txt


:now we prepare the header's...
ECHO Site > header.txt
type GPOs.txt | sed ":a;N;$!ba;s/\n/,/g" >> header.txt
sed -i ":a;N;$!ba;s/\n//g" header.txt
type header.txt > desktop-gpos.csv

:print the ou's into the desktop-gpos.csv
type ou.txt >> desktop-gpos.csv


SETLOCAL ENABLEDELAYEDEXPANSION

for /f "tokens=*" %%A IN ('type "desktop-GPO-links.txt" ^| Grep.exe -E "Path"') DO (
for /f "tokens=*" %%a in ('type gpos.txt') DO (
awk "/%%A/,/-- Who/" "desktop-GPO-links.txt" | grep.exe -E "%%a$"
IF '!ERRORLEVEL!' EQU '0' sed.exe -i "/%%A/s|$|,x|g" desktop-gpos.csv
IF '!ERRORLEVEL!' EQU '1' sed.exe -i "/%%A/s|$|,|g" desktop-gpos.csv
)
)


:last we know need to exchange the ,OU and ,DC commas to something else because
:excel will automatically change them to new columns.
sed.exe -i "s/,OU/;OU/g" desktop-gpos.csv
sed.exe -i "s/,DC/;OU/g" desktop-gpos.csv

:and we remove the "Path " string just for nice-ness:
sed -i -r "s/Path.+Desktops/Desktops/g" desktop-gpos.csv

del sed* /q
```

This generates the following file:

```shell
Site ,Windows Update Policy - Workstations,Fabrikcom Workstation Policy,Local Administrator Account - Workstations,Fabrikcom Workstation Policy V2,Fabrikcom IE 7,Windows - Configure Kerberos to use TCP instead of UDP,Default Domain Policy,DisableAutoArchiveOutlook,Fabrikcom Internal Wireless,General Desktop Policy,[Unknown],PowerSchemeOptions,Fabrikcom IT User Policy,GPO_ORG_Outlook Cache Mode Settings
Desktops;OU=New Town;OU=BigCompanyA;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=SanFrancisco;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyC;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=NewYork;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=GreekTown;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Houston;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Alexandria;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=GreekTown;OU=BigCompanyB;OU=fabrikcom;OU=com,x,,x,x,,x,x,x,x,x,x,,,
Desktops;OU=Okotoks;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Victoria;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Winnipeg;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Malahat;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Toronto;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Calgary;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Vancouver;OU=BigCompanyC;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=SanJose;OU=BigCompanyC;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=Houston;OU=BigCompanyC;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=GoMax;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Richmond;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Kelowna;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Edmonton;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=BigCompanyB-Hamilton;OU=BigCompanyB;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
Desktops;OU=ITGroup;OU=fabrikcom;OU=com,x,x,x,x,,x,x,x,x,,x,,,
```

Which looks like this when you put it in Excel:  
[<img id="BLOGGER_PHOTO_ID_5634426101122710546" style="display: block; margin: 0px auto 10px; text-align: center; cursor: hand; width: 400px; height: 152px;" src="http://2.bp.blogspot.com/-Q9lN76kofAY/TjGAr76bvBI/AAAAAAAAAHE/hRovkGJ-M7U/s400/finalresult.GIF" alt="" border="0" />](http://2.bp.blogspot.com/-Q9lN76kofAY/TjGAr76bvBI/AAAAAAAAAHE/hRovkGJ-M7U/s1600/finalresult.GIF)

Nice and pretty and if you add conditional formatting on "x" you can easily identify which OU's are the same and can be consolidated, or just a nice report on which GPO's are affecting which OU's.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->