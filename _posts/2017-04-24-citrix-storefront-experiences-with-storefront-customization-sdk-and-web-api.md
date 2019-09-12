---
id: 2150
title: Citrix StoreFront - Experiences with Storefront Customization SDK and Web API
date: 2017-04-24T12:43:41-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2150
permalink: /2017/04/24/citrix-storefront-experiences-with-storefront-customization-sdk-and-web-api/
image: /wp-content/uploads/2017/04/Icon.png
categories:
  - Blog
tags:
  - Citrix
  - Internet Explorer
  - PowerShell
  - Storefront
  - Web Interface

  - XenDesktop
---
Our organization has been exploring upgrading our Citrix environment from 6.5 to 7.X.  The biggest road blocks we've been experiencing?  Various _nuanced_ features in Citrix XenApp 6.5 don't work or are not supported in 7.X.

This brings me to this post and an example of the difficulties we are facing, my exploration of solutions to this problem, and our potential solution.

In Citrix Web Interface 5.1+ you can create a URI with a set of application launch parameters and those parameters would be passed to the application.  This is [detailed in this Citrix article here (CTX123998)](https://support.citrix.com/article/CTX123998).  For us, these launches occur with a Site (WI5.4 terminology) or Store (Storefront terminology) that allows anonymous (or unauthenticated) users.

By following that guide and modifying your Web Interface you can change one of your sites so that it accepts launch parameters.  You simply enter a specific URI in your browser, and the application would launch with said parameters.  An example:

<span style="color: #3366ff;">http://bottheory.local/Citrix/&/site/launcher.aspx?CTX_Application=Citrix.MPS.App.&.Notepad&LaunchId=1263894298505&NFuse_AppCommandLine=C:\Windows\WindowsUpdate.log</span>

This allows you to send links around to other people and they can click to automatically launch an application with the parameters specified.  If you are really unlucky, your org might document this as an official way to launch a hosted application and this actually gets coded into certain applications.  So now you may have tens of _local_ apps utilizing this as an acceptable method to launch a _hosted_ application.  For an organization, this _may be_ an acceptable way to launch certain hosted applications since around ~2008 so this "feature", unfortunately, has built up quite a bit of inertia.

We can't let this feature break when we move to StoreFront.  We track the number of launches from these hosted applications and it's in the hundreds/thousands per day.  This is a critical and well used feature.

So how does this work in Web Interface and what actually happens?


![](/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.30.40-PM-1600x1209.png)  
*URI substitution and launch process*



The modifications that you apply add a new 'query' to the URI that is picked up.  This 'query' is "NFuse_AppCommandLine" and the value it is equal to ("C:\Windows\WindowsUpdate.log" in my example) is passed into the ICA file.

The ICA file, when launched, will pass the parameter to a special string "%\*\*" the is set on the command line of the published application.  This token "%\*\*" gets replaced by the parameter specified in the ICA which then generates the launch string.  This string is executed and the result is the program launches.

Can we do this with Storefront?  Well, first things first, is this even possible with XenApp/XenDesktop 7.X?  In order to test this I created an application and specified the special token.

<img class="aligncenter size-full wp-image-2153" src="/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM.png" alt="" width="795" height="580" srcset="/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM.png 795w, /wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM-300x219.png 300w, /wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM-768x560.png 768w" sizes="(max-width: 795px) 100vw, 795px" /> 

I then generated an ICA file and manually modified the LongCommandLine to add my parameter.  I then launched it:

<img class="aligncenter size-full wp-image-2154" src="/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM.png" alt="" width="567" height="943" srcset="/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM.png 567w, /wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM-180x300.png 180w" sizes="(max-width: 567px) 100vw, 567px" /> 

&nbsp;

Did it work?

![](/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.54.46-PM.png)  
Yes, it worked.


Success!  So XenApp/XenDesktop 7.X will substitute the tokens with the LongCommandLine in the ICA file.  Excellent.  So now we just need Storefront to take the URI and add it to the LongCommandLine.  Storefront does not do this out of the box.

However, Citrix offers a couple of possible solutions to this problem (that I explored).

[StoreFront WebAPI](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-web-api.html)  
[StoreFront Store Customization SDK](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-customization-api.html)

What are these and how do they work?

# StoreFront Web API

This API is billed as:

## "Write a new Web UI or integrate StoreFront into your own Web portal"

We are going to need to either modify Storefront or write our own in order to take and parameters in the URI.  I decided to write our own.  Using the ApiExample.html in the WebAPI I added the following:


```javascript
var getUrlParameter = function getUrlParameter(sParam) {
		var sPageURL = decodeURIComponent(window.location.search.substring(1)),
			sURLVariables = sPageURL.split('&'),
			sParameterName,
			i;

		for (i = 0; i < sURLVariables.length; i++) {
			sParameterName = sURLVariables[i].split('=');

			if (sParameterName[0] === sParam) {
				return sParameterName[1] === undefined ? true : sParameterName[1];
			}
		}
	};
	
	var NFuse_AppCommandLine = getUrlParameter('NFuse_AppCommandLine');
	var CTX_Application = getUrlParameter('CTX_Application')
```


This looks into the URI and allows you to get the value of either query string.  In my example I am grabbing the values for "CTX\_Application" and "NFuse\_AppCommandLine".

I then removed a bunch of the authentication portions from the ApiExample.html (I'll be using an unauthenticated store).  In order to automatically select the specified application I added some javascript to check the resource list and get the launch URL:


```javascript
function listResourcesSuccess(data) {
        resourcesData = data.resources;

        for (var i = 0; i < resourcesData.length; i++) {
	    if (resourcesData[i].name == CTX_Application) {
	        console.log("Name Matches");
	        console.log(resourcesData[i]);
	        prepareLaunch(resourcesData[i]);
	    }
        }
    
```


There is a function within the ApiExample.html file that pulls the ICA file.  Could we take this and modify it before returning it with the LongCommandLine addition?  We have the NFuse_AppCommandLine now.

It turns out, you can. You can capture the ICA file as a variable and then using javascripts '.replace' method you can modify the line so that it contains your string.  But how do you pass the ICA file to the system to launch?

This is how Citrix launches the ICA file in the ApiExample.html:


```javascript
// To initiate a launch, an ICA file is loaded into a hidden iframe.
        // The ICA file is returned with content type "application/x-ica", allowing it to be intercepted by the Citrix HDX
        // browser plug-in in Firefox/Chrome/Safari. For IE, the user may be prompted to open the ICA file.
        $('#hidden-iframes').append('<iframe id="' + frameId + '" name="' + frameId + '"></iframe>');

        if (csrfToken != null) {
            icaFileUrl = updateQueryString(icaFileUrl, "CsrfToken", csrfToken);
        }

        // Web Proxy request to load the ICA file into an iframe
        // The request is made by adding
        icaFileUrl = updateQueryString(icaFileUrl, 'launchId', currentTime);
        $("#" + frameId).attr('src', icaFileUrl)
```


It generates a URL then sets it as the src in the iframe.  The actual result looks like this:


```javascript
<iframe id="launchframe_1493045843944" name="launchframe_1493045843944" src="Resources/LaunchIca/QUhTQ1RYLk5vdGVwYWQtWGVuQXBwNjUgSGVucnk-.ica?CsrfToken=DB1E8E5D042DB7226C2635FCB855BF3C&amp;launchId=1493045843944"></iframe
```


The ICA file is returned in this line:


```plaintext
src="Resources/LaunchIca/QUhTQ1RYLk5vdGVwYWQtWGVuQXBwNjUgSGVucnk-.ica?CsrfToken=DB1E8E5D042DB7226C2635FCB855BF3C&amp;launchId=1493045843944
```


When we capture the ICA file as a variable the only way that I've found you can reference it is via a [blob](https://developer.mozilla.org/en/docs/Web/API/Blob).  What does the src path look like when do that?


```plaintext
src="blob:http://bottheory.local/3f19b9e1-403e-4ab7-b741-a4c77a486b95
```


Ok, this looks great!  I can create an ICA file than modify it all through WebAPI and return the ICA file to the browser for execution.  Does it work?

Yes and no.

It works in Chrome and Firefox, but IE doesn't auto-launch.  It prompts to 'save' a file.  Why?  [IE doesn't support opening 'non-standard' blobs](https://connect.microsoft.com/IE/feedback/details/1021584/url-createobjecturl-blob-dontt-work-for-pdf).  MS offers a method called "msSaveOrOpenBlob" which you can use instead, and this method then prompts for opening the blob.  This will work for opening the ICA file but now the end user requires an extra step.  So this won't work.  It needs to be automatic like its supposed to be for a good experience.

So WebAPI appears to offer part of the solution.  We can capture the nFuse_AppCommandLine but we need to get it to LongCommandLine.

At this point I decided to look at the [StoreFront Store Customization SDK](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-customization-api.html).  It states it has this ability:

<p style="padding-left: 30px;">
  <b>Post-Launch ICA file</b> - use this to modify the generated ICA file. For example, use this to change ICA virtual channel parameters and prevent users from accessing their clipboard.
</p>

That sounds perfect!

# StoreFront Store Customization SDK

The StoreFront store customization SDK bills one of its features as:

## The Store Customization SDK allows you to apply custom logic to the process of displaying resources to users and to <span style="text-decoration: underline;">adjust launch parameters</span>. For example, you can use the SDK to control which apps and desktops are displayed to users, to change ICA virtual channel parameters, or to modify access conditions through XenApp and XenDesktop policy selection.

I underlined the part that is important to me.  That's what I want, exactly!  I want to adjust launch parameters.

To start, one of the tasks that I need is to take my nFuse_AppCommandLine and get it passed to the SDK.  The only way I've found to make this [happen is to enable 'forwardedHeaders](https://www.citrix.com/blogs/2015/06/30/whats-new-in-storefront-3-0/)' in your web.config file.


```xhtml
        <communication attempts="1" timeout="00:03:00" loopback="On" loopbackPortUsingHttp="80">
          <proxy enabled="false" processName="Fiddler" port="8888" />
          <forwardedHeaders>
            <header name="nFuseAppCMDLine" />
          </forwardedHeaders>
        </communication
```


With this, you need to set your POST/GET Header on your request to Storefront to get this parameter passed to the SDK.  Here is how I setup my SDK:

Install the "[Microsoft Visual Studio Community Edition](https://www.visualstudio.com/downloads/)" on a test StoreFront server.

<img class="aligncenter size-full wp-image-2156" src="/wp-content/uploads/2017/04/VS_Install.png" alt="" width="1250" height="634" srcset="/wp-content/uploads/2017/04/VS_Install.png 1250w, /wp-content/uploads/2017/04/VS_Install-300x152.png 300w, /wp-content/uploads/2017/04/VS_Install-768x390.png 768w" sizes="(max-width: 1250px) 100vw, 1250px" /> 

&nbsp;

Download the StoreCustomizationSDK and open the 'Customization_Launch' project file:

<img class="aligncenter size-full wp-image-2157" src="/wp-content/uploads/2017/04/customization_launch.png" alt="" width="792" height="266" srcset="/wp-content/uploads/2017/04/customization_launch.png 792w, /wp-content/uploads/2017/04/customization_launch-300x101.png 300w, /wp-content/uploads/2017/04/customization_launch-768x258.png 768w" sizes="(max-width: 792px) 100vw, 792px" /> 

&nbsp;

![](/wp-content/uploads/2017/04/Customzation_Launch_Properties.png)  
Right-click 'Customization_Launch' and select 'Properties'.

&nbsp;

Modify the Outpath in Build to the "%site%\bin" location.  This is NOT the %site%<span style="text-decoration: underline;">Web</span>, but just the %site%.

<img class="aligncenter size-full wp-image-2160" src="/wp-content/uploads/2017/04/SDK_CustomizationLaunch.png" alt="" width="1006" height="778" srcset="/wp-content/uploads/2017/04/SDK_CustomizationLaunch.png 1006w, /wp-content/uploads/2017/04/SDK_CustomizationLaunch-300x232.png 300w, /wp-content/uploads/2017/04/SDK_CustomizationLaunch-768x594.png 768w" sizes="(max-width: 1006px) 100vw, 1006px" /> 

Open the LaunchResultModifer.cs

&nbsp;

<img class="aligncenter size-full wp-image-2161" src="/wp-content/uploads/2017/04/LaunchResultModifer.cs_.png" alt="" width="1281" height="342" srcset="/wp-content/uploads/2017/04/LaunchResultModifer.cs_.png 1281w, /wp-content/uploads/2017/04/LaunchResultModifer.cs_-300x80.png 300w, /wp-content/uploads/2017/04/LaunchResultModifer.cs_-768x205.png 768w" sizes="(max-width: 1281px) 100vw, 1281px" /> 

Set a breakpoint somewhere in the file.

<img class="aligncenter size-full wp-image-2162" src="/wp-content/uploads/2017/04/BreakPoint.png" alt="" width="706" height="175" srcset="/wp-content/uploads/2017/04/BreakPoint.png 706w, /wp-content/uploads/2017/04/BreakPoint-300x74.png 300w" sizes="(max-width: 706px) 100vw, 706px" /> 

Build > Build 'Customization_Launch'

<img class="aligncenter size-full wp-image-2163" src="/wp-content/uploads/2017/04/Build_CustomizationLaunch.png" alt="" width="397" height="135" srcset="/wp-content/uploads/2017/04/Build_CustomizationLaunch.png 397w, /wp-content/uploads/2017/04/Build_CustomizationLaunch-300x102.png 300w" sizes="(max-width: 397px) 100vw, 397px" /> 

Ensure the build was successful:

<img class="aligncenter size-full wp-image-2164" src="/wp-content/uploads/2017/04/Output.png" alt="" width="731" height="124" srcset="/wp-content/uploads/2017/04/Output.png 731w, /wp-content/uploads/2017/04/Output-300x51.png 300w" sizes="(max-width: 731px) 100vw, 731px" /> 

If you get an error message:


```plaintext
---------------------------
Microsoft Visual Studio
---------------------------
A project with an Output Type of Class Library cannot be started directly.

In order to debug this project, add an executable project to this solution which references the library project. Set the executable project as the startup project.
---------------------------
OK 
--------------------------
```


<img class="aligncenter size-full wp-image-2165" src="/wp-content/uploads/2017/04/Error_Message.png" alt="" width="481" height="222" srcset="/wp-content/uploads/2017/04/Error_Message.png 481w, /wp-content/uploads/2017/04/Error_Message-300x138.png 300w" sizes="(max-width: 481px) 100vw, 481px" /> 

Just ignore it.  We're compiling a library and not an executable and that's why you get this message.

Now we connect to the debugger to the 'Citrix Delivery Services Resources'.  Select 'Attach to Process...'

<img class="aligncenter size-full wp-image-2166" src="/wp-content/uploads/2017/04/Attach_To_Process.png" alt="" width="388" height="138" srcset="/wp-content/uploads/2017/04/Attach_To_Process.png 388w, /wp-content/uploads/2017/04/Attach_To_Process-300x107.png 300w" sizes="(max-width: 388px) 100vw, 388px" /> 

Select the w3wp.exe process whose user name is 'Citrix Delivery Services Resources'.  You may need to select 'Show processes from all users' and click ' Attach.

<img class="aligncenter size-full wp-image-2168" src="/wp-content/uploads/2017/04/SDK_Debugger-1.png" alt="" width="857" height="609" srcset="/wp-content/uploads/2017/04/SDK_Debugger-1.png 857w, /wp-content/uploads/2017/04/SDK_Debugger-1-300x213.png 300w, /wp-content/uploads/2017/04/SDK_Debugger-1-768x546.png 768w" sizes="(max-width: 857px) 100vw, 857px" /> 

Click 'Attach'.

<img class="aligncenter size-full wp-image-2169" src="/wp-content/uploads/2017/04/attach.png" alt="" width="415" height="238" srcset="/wp-content/uploads/2017/04/attach.png 415w, /wp-content/uploads/2017/04/attach-300x172.png 300w" sizes="(max-width: 415px) 100vw, 415px" /> 

Browse to your website and click to launch an icon:

<img class="aligncenter size-full wp-image-2170" src="/wp-content/uploads/2017/04/Launch_Icon.png" alt="" width="796" height="333" srcset="/wp-content/uploads/2017/04/Launch_Icon.png 796w, /wp-content/uploads/2017/04/Launch_Icon-300x126.png 300w, /wp-content/uploads/2017/04/Launch_Icon-768x321.png 768w" sizes="(max-width: 796px) 100vw, 796px" /> 

Your debugger should pause at the breakpoint:

<img class="aligncenter size-full wp-image-2171" src="/wp-content/uploads/2017/04/Debugger_Trap.png" alt="" width="1391" height="886" srcset="/wp-content/uploads/2017/04/Debugger_Trap.png 1391w, /wp-content/uploads/2017/04/Debugger_Trap-300x191.png 300w, /wp-content/uploads/2017/04/Debugger_Trap-768x489.png 768w" sizes="(max-width: 1391px) 100vw, 1391px" /> 

And you can inspect the values.

At this point I wasn't interested in trying to re-write the ApiExample.html to get this testing underway, I instead used PowerShell to submit my POST's and GET's.  Remember, I'm using an unauthenticated store so I could cut down on the requests sent to StoreFront to get my apps.  I found a script from [Ryan Butler](https://www.techdrabble.com/citrix/21-create-an-ica-file-from-storefront-using-powershell-or-javascript) and made some modifications to it.  I modified it to remove the parameters since I'm doing testing via hardcoding 


```powershell
$unauthurl = "http://bottheory.local/Citrix/SDKTestSiteWeb/"
$appname = "Notepad 2016 - PLB"

write-host "Requesting ICA file. Please Wait..." -ForegroundColor Yellow

#Gets required tokens
$headers = @{
"Accept"='application/xml, text/xml, */*; q=0.01';
"Content-Length"="0";
"X-Citrix-IsUsingHTTPS"="Yes";
"Referer"=$unauthurl;
}
Invoke-WebRequest -Uri ($unauthurl + "Home/Configuration") -MaximumRedirection 0 -Method POST -Headers $headers -SessionVariable SFSession


#Gets resources 
$headers = @{
"Content-Type"='application/x-www-form-urlencoded; charset=UTF-8';
"Accept"='application/json, text/javascript, */*; q=0.01';
"X-Citrix-IsUsingHTTPS"= "Yes";
"Referer"=$unauthurl;
"format"='json&resourceDetails=Default';
}
$content = Invoke-WebRequest -Uri ($unauthurl + "Resources/List") -MaximumRedirection 0 -Method POST -Headers $headers -SessionVariable SFSession


#Get ICA Launch URL
$resources = $content.content | convertfrom-json
$resourceurl = $resources.resources|where{$_.name -like $appname}

#Launch ICA file, attach customheader (nFuseAppCMDLine)
$headers = @{
"Accept"='application/xml, text/xml, */*; q=0.01';
"Content-Length"="0";
"X-Citrix-IsUsingHTTPS"="Yes";
"nFuseAppCMDLine"="C:\Windows\WindowsUpdate.log";
"Referer"=$unauthurl;
}
$ICA = Invoke-WebRequest -Uri ($unauthurl + $resourceurl.launchurl) -MaximumRedirection 0 -Method GET -Headers $headers  -SessionVariable SFSession
$ICA.RawContent
```

Executing the powershell script and examining a custom variable with breakpoint shows us that our header was successfully passed into the file:

<img class="aligncenter size-full wp-image-2172" src="/wp-content/uploads/2017/04/ExecutionWithVariableProperties.png" alt="" width="1054" height="789" srcset="/wp-content/uploads/2017/04/ExecutionWithVariableProperties.png 1054w, /wp-content/uploads/2017/04/ExecutionWithVariableProperties-300x225.png 300w, /wp-content/uploads/2017/04/ExecutionWithVariableProperties-768x575.png 768w" sizes="(max-width: 1054px) 100vw, 1054px" /> 

Awesome!  So can we do something with this?  Citrix has provided a function that does a find/replace of the value you want to modify.  To enable it, we need to add it the ICAFile.cs:

<img class="aligncenter size-full wp-image-2175" src="/wp-content/uploads/2017/04/Add_ExistingItem.png" alt="" width="581" height="259" srcset="/wp-content/uploads/2017/04/Add_ExistingItem.png 581w, /wp-content/uploads/2017/04/Add_ExistingItem-300x134.png 300w" sizes="(max-width: 581px) 100vw, 581px" /> 

Browse to ICAFile.cs

<img class="aligncenter size-full wp-image-2176" src="/wp-content/uploads/2017/04/helper_icafile.png" alt="" width="935" height="346" srcset="/wp-content/uploads/2017/04/helper_icafile.png 935w, /wp-content/uploads/2017/04/helper_icafile-300x111.png 300w, /wp-content/uploads/2017/04/helper_icafile-768x284.png 768w" sizes="(max-width: 935px) 100vw, 935px" /> 

<img class="aligncenter size-full wp-image-2177" src="/wp-content/uploads/2017/04/ica_file_in_project.png" alt="" width="456" height="205" srcset="/wp-content/uploads/2017/04/ica_file_in_project.png 456w, /wp-content/uploads/2017/04/ica_file_in_project-300x135.png 300w" sizes="(max-width: 456px) 100vw, 456px" /> 

&nbsp;

At this point we can use the helper methods within the IcaFile.cs.  To do so, just add "using Examples.Helpers;" to the top of the file:

<img class="aligncenter size-full wp-image-2178" src="/wp-content/uploads/2017/04/Using_Examples.png" alt="" width="640" height="237" srcset="/wp-content/uploads/2017/04/Using_Examples.png 640w, /wp-content/uploads/2017/04/Using_Examples-300x111.png 300w" sizes="(max-width: 640px) 100vw, 640px" /> 

I cleaned out the HDXRouting out of the file, grabbed the nFuseAppCMDLine header, and then returned the result:


```csharp
/*************************************************************************
*
* Copyright (c) 2013-2015 Citrix Systems, Inc. All Rights Reserved.
* You may only reproduce, distribute, perform, display, or prepare derivative works of this file pursuant to a valid license from Citrix.
*
* THIS SAMPLE CODE IS PROVIDED BY CITRIX "AS IS" AND ANY EXPRESS OR IMPLIED
* WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
* MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
*
*************************************************************************/

using Citrix.DeliveryServices.ResourcesCommon.Customization.Contract;
using Examples.Helpers;

namespace StoreCustomization_Launch
{
    // It is not necessary to implement both ILaunchResultModifier and IHdxRoutingModifier if only customizing
    // the ICA file generation or only customizing the HDX Routing customization.
    public class LaunchResultModifier : ILaunchResultModifier
    {
        public bool RunExtendedValidation { get { return false; } }
        public bool ReturnOriginalValueOnFailure { get { return false; } }

        #region ICA File customization

        public string Modify(string valueToModify, CustomizationContextData context)
        {
            Tracer.TraceInfo("Resource SDK: Launch customization point: ICA file modification");

            var icadetails = new IcaFile(valueToModify);

            //get header we pass in as a variable.
            var nFuseAppCMDLine = context.HttpContext.Request.Headers["nFuseAppCMDLine"];

            //set LongCommandLine to the header variable
            icadetails.SetPropertyValue(IcaFile.ApplicationSection, "LongCommandLine", nFuseAppCMDLine);

            // get modified string back from helper breakdown class.
            string modifiedIcaFile = icadetails.ToString();

            //return the result
            return modifiedIcaFile;
        }

        #endregion
    }
}
```

The result?

<img class="aligncenter size-full wp-image-2179" src="/wp-content/uploads/2017/04/ICAResult.png" alt="" width="1220" height="665" srcset="/wp-content/uploads/2017/04/ICAResult.png 1220w, /wp-content/uploads/2017/04/ICAResult-300x164.png 300w, /wp-content/uploads/2017/04/ICAResult-768x419.png 768w" sizes="(max-width: 1220px) 100vw, 1220px" /> 

Success!  We've used the WebAPI and the StoreFront Customization SDK to supply a header, modify the ICA file, and return it with the value needed!  The ICA file returned works perfectly!  Ok, so I'm thinking this looks pretty good right?!  We just need to get the iframe to supply a custom header when the src gets updated.

Except I don't think it's possible to tag a custom header when you update a src in an iframe.  So then I started thinking maybe I can use a cookie!  If I can pass a cookie into the Storefront Customization SDK I could use that instead of the header.  Unfortunately, I do not see anyway to query a custom cookie or pass a custom cookie into the SDK.  Citrix appears to have (rightfully!) locked down Storefront that this doesn't seem possible.  No custom cookies are passed into the httpcontext (that I can see).

So all this work appears to be for naught (outside of an exercise on how one could setup and customize Storefront).

&nbsp;

But what about the Storefront feature "[add shortcuts to websites](http://docs.citrix.com/en-us/storefront/3/manage-citrix-receiver-for-web-site/sf-configure-site.html?_ga=1.241941066.975940291.1416084467)"?

Although doing that will allow to launch an app via a URL, it doesn't offer any method to pass values into the URL.  So it's a non-starter as well.

Next will be a workaround/solution that appears to work...

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->