---
id: 2150
title: 'Citrix StoreFront &#8211; Experiences with Storefront Customization SDK and Web API'
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
  - XenApp
  - XenDesktop
---
Our organization has been exploring upgrading our Citrix environment from 6.5 to 7.X. ¬ The biggest road blocks we&#8217;ve been experiencing? ¬ Various¬ _nuanced_ features in Citrix XenApp 6.5 don&#8217;t work or are not supported in 7.X.

This brings me to this post and an example of the difficulties we are facing, my exploration of solutions to this problem, and our potential solution.

In Citrix Web Interface 5.1+ you can create a URI with a set of application launch parameters and those parameters would be passed to the application. ¬ This is [detailed in this Citrix article here (CTX123998)](https://support.citrix.com/article/CTX123998). ¬ For us, these launches occur with a Site (WI5.4 terminology) or Store (Storefront terminology) that allows anonymous (or unauthenticated) users.

By following that guide and modifying your Web Interface you can change one of your sites so that it accepts launch parameters. ¬ You simply enter a specific URI in your browser, and the application would launch with said parameters. ¬ An example:

<span style="color: #3366ff;">http://bottheory.local/Citrix/XenApp/site/launcher.aspx?CTX_Application=Citrix.MPS.App.XenApp.Notepad&LaunchId=1263894298505&NFuse_AppCommandLine=C:\Windows\WindowsUpdate.log</span>

This allows you to send links around to other people and they can click to automatically launch an application with the parameters specified. ¬ If you are really unlucky, your org might¬ document this as an official way to launch a hosted¬ application and this actually gets coded into certain applications. ¬ So now you may have tens of _local¬_ apps utilizing this as an acceptable method to launch a¬ _hosted_ application. ¬ For an organization, this¬ _may be_¬ an acceptable way to launch certain hosted applications since around ~2008 so this &#8220;feature&#8221;, unfortunately, has built up quite¬ a bit of inertia.

We can&#8217;t let this feature break when we move to StoreFront. ¬ We track the number of launches from these hosted applications and it&#8217;s in the hundreds/thousands per day. ¬ This is a critical and well used feature.

So how does this work in Web Interface and what actually happens?

<div id="attachment_2152" style="width: 1150px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2152" class="wp-image-2152 size-large" src="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.30.40-PM-1600x1209.png" alt="" width="1140" height="861" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.30.40-PM-1600x1209.png 1600w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.30.40-PM-300x227.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.30.40-PM-768x580.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /></p> 
  
  <p id="caption-attachment-2152" class="wp-caption-text">
    URI substitution and launch process
  </p>
</div>

The modifications that you apply add a new &#8216;query&#8217; to the URI that is picked up. ¬ This &#8216;query&#8217; is &#8220;NFuse_AppCommandLine&#8221; and the¬ value it is¬ equal to (&#8220;C:\Windows\WindowsUpdate.log&#8221; in my example) is passed into the ICA file.

The ICA file, when launched, will pass the parameter to a special string &#8220;%\*\*&#8221; the is set on the command line of the published application. ¬ This token &#8220;%\*\*&#8221; gets replaced by the parameter specified in the ICA which then generates the launch string. ¬ This string is executed and the result is the program launches.

Can we do this with Storefront? ¬ Well, first things first, is this even possible with XenApp/XenDesktop 7.X? ¬ In order to test this I created an application and specified the special token.

<img class="aligncenter size-full wp-image-2153" src="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM.png" alt="" width="795" height="580" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM.png 795w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM-300x219.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.39.21-PM-768x560.png 768w" sizes="(max-width: 795px) 100vw, 795px" /> 

I then generated an ICA file and manually modified the LongCommandLine to add my parameter. ¬ I then launched it:

<img class="aligncenter size-full wp-image-2154" src="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM.png" alt="" width="567" height="943" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM.png 567w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.53.21-PM-180x300.png 180w" sizes="(max-width: 567px) 100vw, 567px" /> 

&nbsp;

Did it work?

<div id="attachment_2155" style="width: 888px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2155" class="wp-image-2155 size-full" src="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.54.46-PM.png" alt="" width="878" height="626" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.54.46-PM.png 878w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.54.46-PM-300x214.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Screen-Shot-2017-04-22-at-1.54.46-PM-768x548.png 768w" sizes="(max-width: 878px) 100vw, 878px" /></p> 
  
  <p id="caption-attachment-2155" class="wp-caption-text">
    Yes, it worked.
  </p>
</div>

Success! ¬ So XenApp/XenDesktop 7.X will substitute the tokens with the LongCommandLine in the ICA file. ¬ Excellent. ¬ So now we just need Storefront to take the URI and add it to the LongCommandLine. ¬ Storefront does not do this out of the box.

However, Citrix offers a couple of¬ possible solutions to this problem (that I explored).

[StoreFront WebAPI](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-web-api.html)  
[StoreFront Store¬ Customization SDK](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-customization-api.html)

What are these and how do they work?

# StoreFront Web API

This API is billed as:

## &#8220;Write a new Web UI or integrate StoreFront into your own Web portal&#8221;

We are going to need to either modify Storefront or write our own in order to take and parameters in the URI. ¬ I decided to write our own. ¬ Using the ApiExample.html in the WebAPI I added¬ the following:

<pre class="lang:js decode:true">var getUrlParameter = function getUrlParameter(sParam) {
		var sPageURL = decodeURIComponent(window.location.search.substring(1)),
			sURLVariables = sPageURL.split('&'),
			sParameterName,
			i;

		for (i = 0; i &lt; sURLVariables.length; i++) {
			sParameterName = sURLVariables[i].split('=');

			if (sParameterName[0] === sParam) {
				return sParameterName[1] === undefined ? true : sParameterName[1];
			}
		}
	};
	
	var NFuse_AppCommandLine = getUrlParameter('NFuse_AppCommandLine');
	var CTX_Application = getUrlParameter('CTX_Application');</pre>

This looks into the URI and allows you to get the value of either query string. ¬ In my example I am grabbing the values for &#8220;CTX\_Application&#8221; and &#8220;NFuse\_AppCommandLine&#8221;.

I then removed a bunch of the authentication portions from the ApiExample.html (I&#8217;ll be using an unauthenticated store). ¬ In order to automatically select the specified application I added some javascript to check the resource list and get the launch URL:

<pre class="lang:default decode:true ">function listResourcesSuccess(data) {
        resourcesData = data.resources;

        for (var i = 0; i &lt; resourcesData.length; i++) {
	    if (resourcesData[i].name == CTX_Application) {
	        console.log("Name Matches");
	        console.log(resourcesData[i]);
	        prepareLaunch(resourcesData[i]);
	    }
        }
    }</pre>

There is a function within the ApiExample.html file that pulls the ICA file. ¬ Could we take this and modify it before returning it with the LongCommandLine addition? ¬ We have the NFuse_AppCommandLine now.

It turns out, you can. You can capture the ICA file as a variable and then using javascripts &#8216;.replace&#8217; method you can modify the line so that it contains your string. ¬ But how do you pass the ICA file to the system to launch?

This is how Citrix launches the ICA file in the ApiExample.html:

<pre class="lang:js decode:true ">// To initiate a launch, an ICA file is loaded into a hidden iframe.
        // The ICA file is returned with content type "application/x-ica", allowing it to be intercepted by the Citrix HDX
        // browser plug-in in Firefox/Chrome/Safari. For IE, the user may be prompted to open the ICA file.
        $('#hidden-iframes').append('&lt;iframe id="' + frameId + '" name="' + frameId + '"&gt;&lt;/iframe&gt;');

        if (csrfToken != null) {
            icaFileUrl = updateQueryString(icaFileUrl, "CsrfToken", csrfToken);
        }

        // Web Proxy request to load the ICA file into an iframe
        // The request is made by adding
        icaFileUrl = updateQueryString(icaFileUrl, 'launchId', currentTime);
        $("#" + frameId).attr('src', icaFileUrl);</pre>

It generates a URL then sets it as the src in the iframe. ¬ The actual result looks like this:

<pre class="lang:default decode:true ">&lt;iframe id="launchframe_1493045843944" name="launchframe_1493045843944" src="Resources/LaunchIca/QUhTQ1RYLk5vdGVwYWQtWGVuQXBwNjUgSGVucnk-.ica?CsrfToken=DB1E8E5D042DB7226C2635FCB855BF3C&amp;launchId=1493045843944"&gt;&lt;/iframe&gt;</pre>

The ICA file is returned in this line:

<pre class="lang:default decode:true">src="Resources/LaunchIca/QUhTQ1RYLk5vdGVwYWQtWGVuQXBwNjUgSGVucnk-.ica?CsrfToken=DB1E8E5D042DB7226C2635FCB855BF3C&amp;launchId=1493045843944"</pre>

When we capture the ICA file as a variable the only way that I&#8217;ve found you can reference it is via a [blob](https://developer.mozilla.org/en/docs/Web/API/Blob). ¬ What does the src path look like when do that?

<pre class="lang:default decode:true">src="blob:http://bottheory.local/3f19b9e1-403e-4ab7-b741-a4c77a486b95"</pre>

Ok, this looks great! ¬ I can create an ICA file than modify it all through WebAPI and return the ICA file to the browser for execution. ¬ Does it work?

Yes and no. üôÅ

It works in Chrome and Firefox, but IE doesn&#8217;t auto-launch. ¬ It prompts to &#8216;save&#8217; a file. ¬ Why? ¬ [IE doesn&#8217;t support opening &#8216;non-standard&#8217; blobs](https://connect.microsoft.com/IE/feedback/details/1021584/url-createobjecturl-blob-dontt-work-for-pdf). ¬ MS offers a method called &#8220;msSaveOrOpenBlob&#8221; which you can use instead, and this method then prompts for opening the blob. ¬ This will work for opening the ICA file but now the end user requires an extra step. ¬ So this won&#8217;t work. ¬ It needs to be automatic like its supposed to be for a good experience.

So WebAPI appears to offer part of the solution. ¬ We can capture the nFuse_AppCommandLine but we need to get it to LongCommandLine.

At this point I decided to look at the [StoreFront Store Customization SDK](https://www.citrix.com/community/citrix-developer/storefront-receiver/store-customization-api.html). ¬ It states it has this ability:

<p style="padding-left: 30px;">
  <b>Post-Launch ICA file</b>‚Äîuse this to modify the generated ICA file. For example, use¬ this to change ICA virtual channel parameters and prevent users from accessing their¬ clipboard.
</p>

That sounds perfect!

# StoreFront Store Customization SDK

The StoreFront store customization SDK bills one of its features as:

## The Store Customization SDK allows you to apply custom logic to the process of displaying resources to users and to <span style="text-decoration: underline;">adjust launch parameters</span>. For example, you can use the SDK to control which apps and desktops are displayed to users, to change ICA virtual channel parameters, or to modify access conditions through XenApp and XenDesktop policy selection.

I underlined the part that is important to me. ¬ That&#8217;s what I want, exactly! ¬ I want to adjust launch parameters.

To start, one of the tasks that I need is to take my nFuse_AppCommandLine and get it passed to the SDK. ¬ The only way I&#8217;ve found to make this [happen is to enable &#8216;forwardedHeaders](https://www.citrix.com/blogs/2015/06/30/whats-new-in-storefront-3-0/)&#8216; in your web.config file.

<pre class="lang:xhtml decode:true ">&lt;communication attempts="1" timeout="00:03:00" loopback="On" loopbackPortUsingHttp="80"&gt;
          &lt;proxy enabled="false" processName="Fiddler" port="8888" /&gt;
          &lt;forwardedHeaders&gt;
            &lt;header name="nFuseAppCMDLine" /&gt;
          &lt;/forwardedHeaders&gt;
        &lt;/communication&gt;</pre>

With this, you need to set your POST/GET Header on your request to Storefront to get this parameter passed to the SDK. ¬ Here is how I setup my SDK:

Install the &#8220;[Microsoft Visual Studio¬ Community Edition](https://www.visualstudio.com/downloads/)&#8221; on a test StoreFront server.

<img class="aligncenter size-full wp-image-2156" src="http://theorypc.ca/wp-content/uploads/2017/04/VS_Install.png" alt="" width="1250" height="634" srcset="http://theorypc.ca/wp-content/uploads/2017/04/VS_Install.png 1250w, http://theorypc.ca/wp-content/uploads/2017/04/VS_Install-300x152.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/VS_Install-768x390.png 768w" sizes="(max-width: 1250px) 100vw, 1250px" /> 

&nbsp;

Download the StoreCustomizationSDK and open the &#8216;Customization_Launch&#8217; project file:

<img class="aligncenter size-full wp-image-2157" src="http://theorypc.ca/wp-content/uploads/2017/04/customization_launch.png" alt="" width="792" height="266" srcset="http://theorypc.ca/wp-content/uploads/2017/04/customization_launch.png 792w, http://theorypc.ca/wp-content/uploads/2017/04/customization_launch-300x101.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/customization_launch-768x258.png 768w" sizes="(max-width: 792px) 100vw, 792px" /> 

&nbsp;

Right-click &#8216;Customization_Launch&#8217; and select &#8216;Properties&#8217;.<img class="aligncenter size-full wp-image-2159" src="http://theorypc.ca/wp-content/uploads/2017/04/Customzation_Launch_Properties.png" alt="" width="341" height="638" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Customzation_Launch_Properties.png 341w, http://theorypc.ca/wp-content/uploads/2017/04/Customzation_Launch_Properties-160x300.png 160w" sizes="(max-width: 341px) 100vw, 341px" />

&nbsp;

Modify the Outpath in Build to the &#8220;%site%\bin&#8221; location. ¬ This is NOT the %site%<span style="text-decoration: underline;">Web</span>, but just the %site%.

<img class="aligncenter size-full wp-image-2160" src="http://theorypc.ca/wp-content/uploads/2017/04/SDK_CustomizationLaunch.png" alt="" width="1006" height="778" srcset="http://theorypc.ca/wp-content/uploads/2017/04/SDK_CustomizationLaunch.png 1006w, http://theorypc.ca/wp-content/uploads/2017/04/SDK_CustomizationLaunch-300x232.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/SDK_CustomizationLaunch-768x594.png 768w" sizes="(max-width: 1006px) 100vw, 1006px" /> 

Open the LaunchResultModifer.cs

&nbsp;

<img class="aligncenter size-full wp-image-2161" src="http://theorypc.ca/wp-content/uploads/2017/04/LaunchResultModifer.cs_.png" alt="" width="1281" height="342" srcset="http://theorypc.ca/wp-content/uploads/2017/04/LaunchResultModifer.cs_.png 1281w, http://theorypc.ca/wp-content/uploads/2017/04/LaunchResultModifer.cs_-300x80.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/LaunchResultModifer.cs_-768x205.png 768w" sizes="(max-width: 1281px) 100vw, 1281px" /> 

Set a breakpoint somewhere in the file.

<img class="aligncenter size-full wp-image-2162" src="http://theorypc.ca/wp-content/uploads/2017/04/BreakPoint.png" alt="" width="706" height="175" srcset="http://theorypc.ca/wp-content/uploads/2017/04/BreakPoint.png 706w, http://theorypc.ca/wp-content/uploads/2017/04/BreakPoint-300x74.png 300w" sizes="(max-width: 706px) 100vw, 706px" /> 

Build > Build &#8216;Customization_Launch&#8217;

<img class="aligncenter size-full wp-image-2163" src="http://theorypc.ca/wp-content/uploads/2017/04/Build_CustomizationLaunch.png" alt="" width="397" height="135" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Build_CustomizationLaunch.png 397w, http://theorypc.ca/wp-content/uploads/2017/04/Build_CustomizationLaunch-300x102.png 300w" sizes="(max-width: 397px) 100vw, 397px" /> 

Ensure the build was successful:

<img class="aligncenter size-full wp-image-2164" src="http://theorypc.ca/wp-content/uploads/2017/04/Output.png" alt="" width="731" height="124" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Output.png 731w, http://theorypc.ca/wp-content/uploads/2017/04/Output-300x51.png 300w" sizes="(max-width: 731px) 100vw, 731px" /> 

If you get an error message:

<pre class="lang:default decode:true ">---------------------------
Microsoft Visual Studio
---------------------------
A project with an Output Type of Class Library cannot be started directly.

In order to debug this project, add an executable project to this solution which references the library project. Set the executable project as the startup project.
---------------------------
OK 
---------------------------</pre>

<img class="aligncenter size-full wp-image-2165" src="http://theorypc.ca/wp-content/uploads/2017/04/Error_Message.png" alt="" width="481" height="222" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Error_Message.png 481w, http://theorypc.ca/wp-content/uploads/2017/04/Error_Message-300x138.png 300w" sizes="(max-width: 481px) 100vw, 481px" /> 

Just ignore it. ¬ We&#8217;re compiling a library and not an executable and that&#8217;s why you get this message.

Now we connect to the debugger to the &#8216;Citrix Delivery Services Resources&#8217;. ¬ Select &#8216;Attach to Process&#8230;&#8217;

<img class="aligncenter size-full wp-image-2166" src="http://theorypc.ca/wp-content/uploads/2017/04/Attach_To_Process.png" alt="" width="388" height="138" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Attach_To_Process.png 388w, http://theorypc.ca/wp-content/uploads/2017/04/Attach_To_Process-300x107.png 300w" sizes="(max-width: 388px) 100vw, 388px" /> 

Select the w3wp.exe process whose user name is &#8216;Citrix Delivery Services Resources&#8217;. ¬ You may need to select &#8216;Show processes from all users&#8217; and click &#8216; Attach.

<img class="aligncenter size-full wp-image-2168" src="http://theorypc.ca/wp-content/uploads/2017/04/SDK_Debugger-1.png" alt="" width="857" height="609" srcset="http://theorypc.ca/wp-content/uploads/2017/04/SDK_Debugger-1.png 857w, http://theorypc.ca/wp-content/uploads/2017/04/SDK_Debugger-1-300x213.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/SDK_Debugger-1-768x546.png 768w" sizes="(max-width: 857px) 100vw, 857px" /> 

Click &#8216;Attach&#8217;.

<img class="aligncenter size-full wp-image-2169" src="http://theorypc.ca/wp-content/uploads/2017/04/attach.png" alt="" width="415" height="238" srcset="http://theorypc.ca/wp-content/uploads/2017/04/attach.png 415w, http://theorypc.ca/wp-content/uploads/2017/04/attach-300x172.png 300w" sizes="(max-width: 415px) 100vw, 415px" /> 

Browse to your website and click to launch an icon:

<img class="aligncenter size-full wp-image-2170" src="http://theorypc.ca/wp-content/uploads/2017/04/Launch_Icon.png" alt="" width="796" height="333" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Launch_Icon.png 796w, http://theorypc.ca/wp-content/uploads/2017/04/Launch_Icon-300x126.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Launch_Icon-768x321.png 768w" sizes="(max-width: 796px) 100vw, 796px" /> 

Your debugger should pause at the breakpoint:

<img class="aligncenter size-full wp-image-2171" src="http://theorypc.ca/wp-content/uploads/2017/04/Debugger_Trap.png" alt="" width="1391" height="886" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Debugger_Trap.png 1391w, http://theorypc.ca/wp-content/uploads/2017/04/Debugger_Trap-300x191.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Debugger_Trap-768x489.png 768w" sizes="(max-width: 1391px) 100vw, 1391px" /> 

And you can inspect the values.

At this point I wasn&#8217;t interested in trying to re-write the ApiExample.html to get this testing underway, I instead used PowerShell to submit my POST&#8217;s and GET&#8217;s. ¬ Remember, I&#8217;m using an unauthenticated store so I could cut down on the requests sent to StoreFront to get my apps. ¬ I found a script from [Ryan Butler](https://www.techdrabble.com/citrix/21-create-an-ica-file-from-storefront-using-powershell-or-javascript) and made some modifications to it. ¬ I modified it to remove the parameters since I&#8217;m doing testing via hardcoding üôÇ

<pre class="lang:ps decode:true">$unauthurl = "http://bottheory.local/Citrix/SDKTestSiteWeb/"
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
</pre>

Executing the powershell script and examining a custom variable with breakpoint shows us that our header was successfully passed into the file:

<img class="aligncenter size-full wp-image-2172" src="http://theorypc.ca/wp-content/uploads/2017/04/ExecutionWithVariableProperties.png" alt="" width="1054" height="789" srcset="http://theorypc.ca/wp-content/uploads/2017/04/ExecutionWithVariableProperties.png 1054w, http://theorypc.ca/wp-content/uploads/2017/04/ExecutionWithVariableProperties-300x225.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/ExecutionWithVariableProperties-768x575.png 768w" sizes="(max-width: 1054px) 100vw, 1054px" /> 

Awesome! ¬ So can we do something with this? ¬ Citrix has provided a function that does a find/replace of the value you want to modify. ¬ To enable it, we need to add it the ICAFile.cs:

<img class="aligncenter size-full wp-image-2175" src="http://theorypc.ca/wp-content/uploads/2017/04/Add_ExistingItem.png" alt="" width="581" height="259" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Add_ExistingItem.png 581w, http://theorypc.ca/wp-content/uploads/2017/04/Add_ExistingItem-300x134.png 300w" sizes="(max-width: 581px) 100vw, 581px" /> 

Browse to ICAFile.cs

<img class="aligncenter size-full wp-image-2176" src="http://theorypc.ca/wp-content/uploads/2017/04/helper_icafile.png" alt="" width="935" height="346" srcset="http://theorypc.ca/wp-content/uploads/2017/04/helper_icafile.png 935w, http://theorypc.ca/wp-content/uploads/2017/04/helper_icafile-300x111.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/helper_icafile-768x284.png 768w" sizes="(max-width: 935px) 100vw, 935px" /> 

<img class="aligncenter size-full wp-image-2177" src="http://theorypc.ca/wp-content/uploads/2017/04/ica_file_in_project.png" alt="" width="456" height="205" srcset="http://theorypc.ca/wp-content/uploads/2017/04/ica_file_in_project.png 456w, http://theorypc.ca/wp-content/uploads/2017/04/ica_file_in_project-300x135.png 300w" sizes="(max-width: 456px) 100vw, 456px" /> 

&nbsp;

At this point we can use the helper methods within the IcaFile.cs. ¬ To do so, just add &#8220;using Examples.Helpers;&#8221; to the top of the file:

<img class="aligncenter size-full wp-image-2178" src="http://theorypc.ca/wp-content/uploads/2017/04/Using_Examples.png" alt="" width="640" height="237" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Using_Examples.png 640w, http://theorypc.ca/wp-content/uploads/2017/04/Using_Examples-300x111.png 300w" sizes="(max-width: 640px) 100vw, 640px" /> 

I cleaned out the HDXRouting out of the file, grabbed the nFuseAppCMDLine header, and then returned the result:

<pre class="lang:c# decode:true ">/*************************************************************************
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
</pre>

The result?

<img class="aligncenter size-full wp-image-2179" src="http://theorypc.ca/wp-content/uploads/2017/04/ICAResult.png" alt="" width="1220" height="665" srcset="http://theorypc.ca/wp-content/uploads/2017/04/ICAResult.png 1220w, http://theorypc.ca/wp-content/uploads/2017/04/ICAResult-300x164.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/ICAResult-768x419.png 768w" sizes="(max-width: 1220px) 100vw, 1220px" /> 

Success! ¬ We&#8217;ve used the WebAPI and the StoreFront Customization SDK to supply a header, modify the ICA file, and return it with the value needed! ¬ The ICA file returned works perfectly! ¬ Ok, so I&#8217;m thinking this looks pretty good right?! ¬ We just need to get the iframe to supply a custom header when the src gets updated.

Except I don&#8217;t think it&#8217;s possible to tag a custom header when you update a src in an iframe. ¬ So then I started thinking maybe I can use a cookie! ¬ If I can pass a cookie into the Storefront Customization SDK I could use that instead of the header. ¬ Unfortunately, I do not see anyway to query a custom cookie or pass a custom cookie into the SDK. ¬ Citrix appears to have (rightfully!) locked down Storefront that this doesn&#8217;t seem possible. ¬ No custom cookies are passed into the httpcontext (that I can see).

So all this work appears to be for naught (outside of an exercise on how one could setup and customize Storefront).

&nbsp;

But what about the Storefront feature &#8220;[add shortcuts¬ to websites](http://docs.citrix.com/en-us/storefront/3/manage-citrix-receiver-for-web-site/sf-configure-site.html?_ga=1.241941066.975940291.1416084467)&#8220;?

Although¬ doing that will allow to launch an app via a URL, it doesn&#8217;t offer any method to pass values into the URL. ¬ So it&#8217;s a non-starter as well.

Next will be a workaround/solution that appears to work&#8230;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->