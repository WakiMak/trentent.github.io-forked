---
id: 2182
title: Citrix Storefront - Pass URI parameters to an application
date: 2017-05-01T14:47:59-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2182
permalink: /2017/05/01/citrix-storefront-pass-uri-parameters-to-an-application/
image: /wp-content/uploads/2017/05/storefront_icon.png
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - PowerShell
  - Storefront
  - Web Interface

  - XenDesktop
---
[In my previous post](https://theorypc.ca/2017/04/24/citrix-storefront-experiences-with-storefront-customization-sdk-and-web-api/), I was exploring takingÂ URI parameters and passing them to an application.

The main issue we are facing is that Storefront launches the ica file via an iframe src. Â When launching the ica via this method the iframe does a simple 'GET' without passing any HEADER parameters - which is the only (documented) way to pass data to Storefront.

What can I do?Â I think what I need to do is create my own \*custom\* launchica command. Â Because this will be against an unauthenticated store we should be able remove the authentication portions AND any unique identifiers (eg, csrf data). Â Really, we just need the two options - the application to launch and the parameter to pass into it. Â I am <span style="text-decoration: underline;"><strong>NOT</strong> </span>a web developer, I do not know what would be the best solution to this problem, but here is something I came up with.

My first thought is this needs to be a URL that must be queried and that URL must return a specific content-type. Â I know Powershell has lots of control over specifying things like this and I have some familiarity with Powershell so I've chosen that as my tool of choice to solve this problem.

In order to start I need to create or find something that will get data from a URL to powershell. Â Fortunately, a brilliant person by the name of [SteveÂ Lee](https://twitter.com/steve_msft) solved this [first problem for me](https://www.powershellgallery.com/packages/HttpListener/1.0.2).

What he created is a Powershell module that creates a HTTP listener than waits for a request. We can take this listener and modify it so it listens for our two variables (CTX_Application and NFuseAppCommandLine) and then returns a ICA file. Â Since this is an unauthenticated URL I had to remove the authentication feature of the script and I added a function to query the real Storefront services to generate the ICA file.

So what I'm envisioning is replacing the "LaunchIca" command with my custom one.

&nbsp;

This is my modification of Steve's script:

<pre class="lang:ps decode:true"># Copyright (c) 2014 Microsoft Corp.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
#
# Modified by Trentent Tye for ICA file returning.

Function ConvertTo-HashTable {
    <#
    .Synopsis
        Convert an object to a HashTable
    .Description
        Convert an object to a HashTable excluding certain types.  For example, ListDictionaryInternal doesn't support serialization therefore
        can't be converted to JSON.
    .Parameter InputObject
        Object to convert
    .Parameter ExcludeTypeName
        Array of types to skip adding to resulting HashTable.  Default is to skip ListDictionaryInternal and Object arrays.
    .Parameter MaxDepth
        Maximum depth of embedded objects to convert.  Default is 4.
    .Example
        $bios = get-ciminstance win32_bios
        $bios | ConvertTo-HashTable
    #>
    
    Param (
        [Parameter(Mandatory=$true,ValueFromPipeline=$true)]
        [Object]$InputObject,
        [string[]]$ExcludeTypeName = @("ListDictionaryInternal","Object[]"),
        [ValidateRange(1,10)][Int]$MaxDepth = 4
    )

    Process {

        Write-Verbose "Converting to hashtable $($InputObject.GetType())"
        #$propNames = Get-Member -MemberType Properties -InputObject $InputObject | Select-Object -ExpandProperty Name
        $propNames = $InputObject.psobject.Properties | Select-Object -ExpandProperty Name
        $hash = @{}
        $propNames | % {
            if ($InputObject.$_ -ne $null) {
                if ($InputObject.$_ -is [string] -or (Get-Member -MemberType Properties -InputObject ($InputObject.$_) ).Count -eq 0) {
                    $hash.Add($_,$InputObject.$_)
                } else {
                    if ($InputObject.$_.GetType().Name -in $ExcludeTypeName) {
                        Write-Verbose "Skipped $_"
                    } elseif ($MaxDepth -gt 1) {
                        $hash.Add($_,(ConvertTo-HashTable -InputObject $InputObject.$_ -MaxDepth ($MaxDepth - 1)))
                    }
                }
            }
        }
        $hash
    }
}

Function Get-ICA {
    <#
    .Synopsis
        Queries An Anonymous Store for an ICA file and sets the LongCommand in the ICA with a URI parameter
    .Description
        This function will take 3 parameters, the store, the program and the additional parameters to pass to the program.
        It will then query and pull the required ICA file then make the substitution and return the result as TEXT.
        You may need to convert the result to application/ica.
    .Parameter Store
        URL to the store.  eg, "https://storefront.mydomain.local/Citrix/unauthWeb/"
    .Parameter Program
        The name of the program to launch.  eg, "Notepad 2016 - PLB"
    .Parameter LongCommand
        The extra command string to pass to the program.  Eg, "C:\Windows\WindowsUpdate.Log"
    .Example
        Get-ICA -Store "http://$env:COMPUTERNAME/Citrix/PLBWeb/" -Program "Notepad 2016 - PLB" -LongCommand "C:\Windows\WindowsUpdate.Log"
    #>
    Param (
    [Parameter()]
    [String] $Store = "",

    [Parameter()]
    [String] $Program = "",
        
    [Parameter()]
    [String] $LongCommand = ""
    )

    Process {

        Write-Verbose "Get-ICA - Gets required tokens"
        #Gets required tokens
        $headers = @{
        "Accept"='application/xml, text/xml, */*; q=0.01';
        "Content-Length"="0";
        "X-Citrix-IsUsingHTTPS"="Yes";
        "Referer"=$Store;
        }
        $result = Invoke-WebRequest -Uri ($Store + "Home/Configuration") -MaximumRedirection 0 -Method POST -Headers $headers -SessionVariable SFSession|Out-Null

        Write-Verbose "Get-ICA - Gets list of resources for the application"
        $headers = @{
        "Content-Type"='application/x-www-form-urlencoded; charset=UTF-8';
        "Accept"='application/json, text/javascript, */*; q=0.01';
        "X-Citrix-IsUsingHTTPS"= "Yes";
        "Referer"=$Store;
        "format"='json&resourceDetails=Default';
        }
        $content = Invoke-WebRequest -Uri ($Store + "Resources/List") -MaximumRedirection 0 -Method POST -Headers $headers -SessionVariable SFSession

        #Creates ICA file
        $resources = $content.content | convertfrom-json
        $resourceurl = $resources.resources | where{$_.name -like $Program}

        if ($resourceurl.count)
        {
            write-host "MULTIPLE APPS FOUND for $Program.  Check APP NAME!" -ForegroundColor Red
            $resourceurl|select id,name
        }
        else
        {
            Write-Verbose "Get-ICA - Getting ICA file"
            $icafile = Invoke-WebRequest -Uri ($Store + $resourceurl.launchurl) -MaximumRedirection 0 -Method GET -Headers $headers  -SessionVariable SFSession
            $icafile = $icafile.ToString()
            Write-Verbose "Get-ICA - adding LongCommand"
            $icafile = $icafile -replace "LongCommandLine=", "LongCommandLine=$LongCommand"
        }
        $icafile
    }
}

Function Start-HTTPListener {
    <#
    .Synopsis
        Creates a new HTTP Listener accepting PowerShell command line to execute
    .Description
        Creates a new HTTP Listener enabling a remote client to execute PowerShell command lines using a simple REST API.
        This function requires running from an elevated administrator prompt to open a port.

        Use Ctrl-C to stop the listener.  You'll need to send another web request to allow the listener to stop since
        it will be blocked waiting for a request.
    .Parameter Port
        Port to listen, default is 8888
    .Parameter URL
        URL to listen, default is /
    .Parameter Auth
        Authentication Schemes to use, default is IntegratedWindowsAuthentication
    .Example
        Start-HTTPListener -Port 80 -Url "Citrix/PLBWeb/ica_launcher" -Auth Anonymous
        Invoke-WebRequest -Uri "http://localhost/Citrix/PLBWeb/ica_launcher?CTX_Application=Notepad%202016%20-%20PLB&NFuse_AppCommandLine=C:\Windows\WindowsUpdate.log" -UseDefaultCredentials | Format-List *
    #>
    
    Param (
        [Parameter()]
        [Int] $Port = 8888,

        [Parameter()]
        [String] $Url = "",
        
        [Parameter()]
        [System.Net.AuthenticationSchemes] $Auth = [System.Net.AuthenticationSchemes]::IntegratedWindowsAuthentication
        )

    Process {
        $ErrorActionPreference = "Stop"

        $CurrentPrincipal = New-Object Security.Principal.WindowsPrincipal( [Security.Principal.WindowsIdentity]::GetCurrent())
        if ( -not ($currentPrincipal.IsInRole( [Security.Principal.WindowsBuiltInRole]::Administrator ))) {
            Write-Error "This script must be executed from an elevated PowerShell session" -ErrorAction Stop
        }

        if ($Url.Length -gt 0 -and -not $Url.EndsWith('/')) {
            $Url += "/"
        }

        $listener = New-Object System.Net.HttpListener
        $prefix = "http://*:$Port/$Url"
        $listener.Prefixes.Add($prefix)
        $listener.AuthenticationSchemes = $Auth 
        try {
            $listener.Start()
            while ($true) {
                $statusCode = 200
                Write-Warning "Note that thread is blocked waiting for a request.  After using Ctrl-C to stop listening, you need to send a valid HTTP request to stop the listener cleanly."
                Write-Warning "Sending 'exit' command will cause listener to stop immediately"
                Write-Verbose "Listening on $port..."
                $context = $listener.GetContext()
                $request = $context.Request
                Write-Verbose "Request = $($request.QueryString)"

 
                if (-not $request.QueryString.HasKeys()) {
                    $commandOutput = "SYNTAX: command=<string> format=[JSON|TEXT|XML|NONE|CLIXML]"
                    $Format = "TEXT"
                } else {
                    
                    #change command to parameters...
                    $CTX_Application = $request.QueryString.Item("CTX_Application")
                    $NFuse_AppCommandLine = $request.QueryString.Item("NFuse_AppCommandLine")

                    #uncomment next portion to allow remote exit of the listener
                    <#
                    if ($CTX_Application -eq "exit") {
                        Write-Verbose "Received command to exit listener"
                        return
                    }
                    #>

                    $Format = $request.QueryString.Item("format")
                    if ($Format -eq $Null) {
                        $Format = "JSON"
                    }

                    Write-Verbose "Application = $CTX_Application"
                    Write-Verbose "NFuse_AppCommandLine = $NFuse_AppCommandLine"
                    Write-Verbose "Format = $Format"


                    Write-Verbose "Executing Command"
                    ## execute command here...  --> ensure you change "PLBWeb" to your proper store
                    $script = get-ica -store "http://$env:COMPUTERNAME/Citrix/PLBWeb/" -Program "$CTX_Application" -LongCommand $NFuse_AppCommandLine
                    write-verbose "are we back yet?"
                        
                    $commandOutput = $script.ToString()
                        

                }
            

            Write-Verbose "Response:"
            if (!$script) {
                $script = [string]::Empty
            }
            Write-Verbose $script

            $response = $context.Response
            $response.StatusCode = $statusCode
            $response.ContentType = "application/x-ica; charset=utf-8"
            $buffer = [System.Text.Encoding]::UTF8.GetBytes($script)

            $response.ContentLength64 = $buffer.Length
            $output = $response.OutputStream
            $output.Write($buffer,0,$buffer.Length)
            $output.Close()
            }
        } finally {
            $listener.Stop()
        }
    }
}</pre>

And the command to start the HTTP listener:

<pre class="lang:ps decode:true ">ipmo "C:\swinst\HttpListener_1.0.1\HttpListener\HTTPListener.psm1"
#Example URL
#http://bottheory.local/Citrix/PLBWeb/ica_launcher?CTX_Application=Notepad%202016%20-%20PLB&NFuse_AppCommandLine=C:\Windows\WindowsUpdate.log
Start-HTTPListener -Port 80 -Url "Citrix/PLBWeb/ica_launcher" -Auth Anonymous</pre>

Eventually, this will need to be converted to a scheduled task or a service. Â When running the listener manually,Â it looks like this:

<img class="aligncenter size-full wp-image-2184" src="http://theorypc.ca/wp-content/uploads/2017/05/RunningHTTPListener.png" alt="" width="979" height="178" srcset="http://theorypc.ca/wp-content/uploads/2017/05/RunningHTTPListener.png 979w, http://theorypc.ca/wp-content/uploads/2017/05/RunningHTTPListener-300x55.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/RunningHTTPListener-768x140.png 768w" sizes="(max-width: 979px) 100vw, 979px" /> 

&nbsp;

I originally planned to use the 'WebAPI' and create a custom StoreFront, but I really, really want to use the new Storefront UI. Â In addition, I do NOT want to have to copy a file around to each Storefront server to enable this feature. Â So I started to wonder if it would be possible to modify Storefront via the extensible customization API's it provides. Â This involves adding javascript to theÂ "C:\inetpub\wwwroot\Citrix\StoreWeb\custom\script.js" and modifying theÂ "C:\inetpub\wwwroot\Citrix\StoreWeb\custom\style.css" files. Â To start, my goal is to mimic our existing functionality and UI to an extent that makes sense.

The Web Interface 5.4 version of this web launcher looked like this:

<img class="aligncenter size-full wp-image-2185" src="http://theorypc.ca/wp-content/uploads/2017/05/WI_UnauthStore.png" alt="" width="951" height="308" srcset="http://theorypc.ca/wp-content/uploads/2017/05/WI_UnauthStore.png 951w, http://theorypc.ca/wp-content/uploads/2017/05/WI_UnauthStore-300x97.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/WI_UnauthStore-768x249.png 768w" sizes="(max-width: 951px) 100vw, 951px" /> 

When you browse to the URL in Web Interface 5.4 the application is automatically launched. Â If it doesn't launch, "click to connect" will launch it for you manually. Â This is the function and features I want.

Storefront, without any modifications, looks like this with an authenticated store:

<img class="aligncenter size-full wp-image-2186" src="http://theorypc.ca/wp-content/uploads/2017/05/unauth_Storefront.png" alt="" width="1136" height="333" srcset="http://theorypc.ca/wp-content/uploads/2017/05/unauth_Storefront.png 1136w, http://theorypc.ca/wp-content/uploads/2017/05/unauth_Storefront-300x88.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/unauth_Storefront-768x225.png 768w" sizes="(max-width: 1136px) 100vw, 1136px" /> 

So, I need to make a few modifications.

  1. I need to hide all applications that are NOT my target application
  2. I need to add the additional messaging "If your application does not appear within a few seconds, <span style="text-decoration: underline;">click to connect.</span>" with the underlined as a URL to our launcher.
  3. I want to minimize the interface by hiding the toolbar. Â Since only one application will be displayed we do not need to see "All | Categories Â Search All Apps"
  4. I want to hide the 'All Apps' text
  5. I want to hide "Details", we're going to keep this UI minimal.

The beauty of Storefront, in its current incarnation, is most of this is CSS modifications. Â I made the following modifications to the CSS to get my UI minimalized:

<pre class="lang:css decode:true ">/* removes "Apps | Categories    Search apps:" tool bar */
.large .myapps-view .store-toolbar, .large .desktops-view .store-toolbar, .large .tasks-view .store-toolbar, .large .store-view .store-toolbar {
	display : none;
}

/* this is to display a single app, we don't need to be told we're looking at 'All Apps' */
.largeTiles .store-view .store-apps-title {
	display : none;
}

/* hide the 'Details' link */
.largeTiles .storeapp-action-link, .taskapp-action-link {
	display : none;
}

/* stretch the app content to 100% of the width of the window */
.storeapp-list .storeapp, .storeapp-list .folder {
	width : 100%;
}

/* enlarge the app name text field to 100% of the screen width -- this allows our messaging to be read in it's entirety. */
.largeTiles .storeapp-name, .large .ruler-container {
	width : 100%;
}
</pre>

This resulted in a UI that looked like this:

<img class="aligncenter size-full wp-image-2187" src="http://theorypc.ca/wp-content/uploads/2017/05/StoreFront_With_CSS.png" alt="" width="1135" height="578" srcset="http://theorypc.ca/wp-content/uploads/2017/05/StoreFront_With_CSS.png 1135w, http://theorypc.ca/wp-content/uploads/2017/05/StoreFront_With_CSS-300x153.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/StoreFront_With_CSS-768x391.png 768w" sizes="(max-width: 1135px) 100vw, 1135px" /> 

So now I want to remove all apps except my targeted application that should come in a query string.

I was curious if the 'script.js' would recognize the URI parameter passed to Storefront. Â I modified my 'script.js' with the following:

<pre class="lang:js decode:true ">// Edit this file to add your customized JavaScript or load additional JavaScript files.

//grab the URL and parse out the parameters we want (application name, launch parameters)
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
var CTX_Application = getUrlParameter('CTX_Application');

console.log("NFuse_AppCommandLine " + NFuse_AppCommandLine);
console.log("CTX_Application " + CTX_Application);
</pre>

Going to my URL and checking the 'Console' in Chrome revealed:

<img class="aligncenter size-full wp-image-2188" src="http://theorypc.ca/wp-content/uploads/2017/05/scriptjs_uri.png" alt="" width="892" height="607" srcset="http://theorypc.ca/wp-content/uploads/2017/05/scriptjs_uri.png 892w, http://theorypc.ca/wp-content/uploads/2017/05/scriptjs_uri-300x204.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/scriptjs_uri-768x523.png 768w" sizes="(max-width: 892px) 100vw, 892px" /> 

Yes, indeed, we are getting the URI parameters!

Great! Â So can we filter our application list to only display the app in the URI?

Citrix [offers a bunch of 'extensions'](https://docs.citrix.com/en-us/storefront/3-5/migrate-wi-to-storefront/receiver-extension-apis.html). Â Can one of them work for our purpose? Â This one sounds interesting:

<pre class="lang:default decode:true ">excludeApp(app)
Exclude an application completely from all UI, even if it would normally be included</pre>

Can we do a simple check that if the application does not equal "CTX_Application" to exclude it?

The function looks like this:

<pre class="lang:js decode:true ">CTXS.Extensions.excludeApp = function(app) {
    // return true or false if we don't match the target app name
	//we only want to show the application name found in the URI
	//if the app name does not equal the URI name then we hide it.
	if (app.name!= CTX_Application) {
		return true;
	}
};</pre>

Did it work?

<img class="aligncenter size-full wp-image-2189" src="http://theorypc.ca/wp-content/uploads/2017/05/Exclude_App.png" alt="" width="595" height="498" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Exclude_App.png 595w, http://theorypc.ca/wp-content/uploads/2017/05/Exclude_App-300x251.png 300w" sizes="(max-width: 595px) 100vw, 595px" /> 

Yes! Â Perfectly! Â Can we append a message to the application? Â Looking at Citrix's extensions this one looks promising:

<pre class="lang:default decode:true ">onAppHTMLGeneration(element)
Called when HTML is generated for one or more app tile, passing the parent container. Intended for deep customization. (Warning this sort of change is likely to be version specific)</pre>

Ok. Â That warning sucks. Â My whole post is based on StoreFront 3.9 so I cannot guarantee these modifications will work in future versions or previous versions. Â Your Mileage May Vary.

So what elements could we manipulate to add our text?

<img class="aligncenter size-full wp-image-2190" src="http://theorypc.ca/wp-content/uploads/2017/05/storeapp-name.png" alt="" width="867" height="370" srcset="http://theorypc.ca/wp-content/uploads/2017/05/storeapp-name.png 867w, http://theorypc.ca/wp-content/uploads/2017/05/storeapp-name-300x128.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/storeapp-name-768x328.png 768w, http://theorypc.ca/wp-content/uploads/2017/05/storeapp-name-750x320.png 750w" sizes="(max-width: 867px) 100vw, 867px" /> 

Could we add another "p class=storeapp-name" (this is just text) for our messaging? Â The onAppHTMLGeneration function says it is returned when the HTML is generated for an app, so what does this look like?

I added the following to script.js:

<pre class="lang:default decode:true ">CTXS.Extensions.onAppHTMLGeneration = function(element) {
	//this function is not guaranteed to work across Storefront versions.  Ensure proper testing is conducted when upgrading.
	//tested on StoreFront 3.9
	console.log(element);
};</pre>

And this was the result in the Chrome Console:

<img class="aligncenter size-full wp-image-2191" src="http://theorypc.ca/wp-content/uploads/2017/05/Chrome_Console.png" alt="" width="1128" height="447" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Chrome_Console.png 1128w, http://theorypc.ca/wp-content/uploads/2017/05/Chrome_Console-300x119.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Chrome_Console-768x304.png 768w" sizes="(max-width: 1128px) 100vw, 1128px" /> 

So this is returning an [DomHTMLElement](https://www.w3schools.com/jsref/dom_obj_all.asp). Â DOMHTMLElements have numerous methods to add/create/append/modify data to them. Â Perfect. Â Doing some research (I'm not a web developer) I found that you can modify the content of an element by this style command:

<pre class="lang:default decode:true">CTXS.Extensions.onAppHTMLGeneration = function(element) {
	//this function is not guaranteed to work across Storefront versions.  Ensure proper testing is conducted when upgrading.
	//tested on StoreFront 3.9
	$( "div.storeapp-details-container" ).append( "whatever text you want here" );
};
</pre>

This results in the following:

<img class="aligncenter size-full wp-image-2192" src="http://theorypc.ca/wp-content/uploads/2017/05/whatevertext.png" alt="" width="413" height="306" srcset="http://theorypc.ca/wp-content/uploads/2017/05/whatevertext.png 413w, http://theorypc.ca/wp-content/uploads/2017/05/whatevertext-300x222.png 300w" sizes="(max-width: 413px) 100vw, 413px" /> 

We have text!

Great.

My preference is to have the text match the application name's format. Â I also wanted to test if I could add a link to the text. Â So I modified my css line:

<pre class="lang:default decode:true">CTXS.Extensions.onAppHTMLGeneration = function(element) {
	//this function is not guaranteed to work across Storefront versions.  Ensure proper testing is conducted when upgrading.
	//tested on StoreFront 3.9
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\"><br></p>" );
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\">If your application does not appear within a few seconds, <a href=\"http://www.google.ca\">click to connect</a></p>" );
};</pre>

The result?

<img class="aligncenter size-full wp-image-2193" src="http://theorypc.ca/wp-content/uploads/2017/05/Link_Working.png" alt="" width="568" height="339" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Link_Working.png 568w, http://theorypc.ca/wp-content/uploads/2017/05/Link_Working-300x179.png 300w" sizes="(max-width: 568px) 100vw, 568px" /> 

Oh man. Â This is looking good. Â My 'click to connect' link isn't working at this point, it just goes to google, but at least I know I can add a URL and have it work! Â Now I just need to generate a URL and set that to replace 'click to connect'.

When I made my HTTPListener I purposefully made it with the following:

<pre class="lang:ps decode:true">Start-HTTPListener -Port 80 -Url "Citrix/PLBWeb/ica_launcher" -Auth Anonymous</pre>

The reason why I had it set to share the url of the Citrix Store is the launchurl generated by Storefront is:

<pre class="lang:default decode:true ">Resources/LaunchIca/WEQ3Lk5vdGVwYWQgMjAxNiAtIFBMQg--.ica</pre>

The full path for the URL is actually:

<pre class="lang:default decode:true">http://bottheory.local/Citrix/PLBWeb/Resources/LaunchIca/WEQ3Lk5vdGVwYWQgMjAxNiAtIFBMQg--.ica</pre>

So the request actually starts at the storename. Â And if I want this to work with a URL re-write service like Netscaler I suspect I need to keep to relative paths. Â So to reach my custom ica_launcher I can just put this in my script.js file:

<pre class="lang:default decode:true ">//generated launch URL
var launchURL = "ica_launcher?CTX_Application=" + CTX_Application + "&NFuse_AppCommandLine=" + NFuse_AppCommandLine</pre>

and then I can replace my 'click to connect' link with:

<pre class="lang:default decode:true ">CTXS.Extensions.onAppHTMLGeneration = function(element) {
	//this function is not guaranteed to work across Storefront versions.  Ensure proper testing is conducted when upgrading.
	//tested on StoreFront 3.9
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\"><br></p>" );
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\">If your application does not appear within a few seconds, <a href=\"" + launchURL + "\">click to connect</a></p>" );
};</pre>

The result?

<img class="aligncenter size-full wp-image-2194" src="http://theorypc.ca/wp-content/uploads/2017/05/hover_link.png" alt="" width="802" height="461" srcset="http://theorypc.ca/wp-content/uploads/2017/05/hover_link.png 802w, http://theorypc.ca/wp-content/uploads/2017/05/hover_link-300x172.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/hover_link-768x441.png 768w" sizes="(max-width: 802px) 100vw, 802px" /> 

The url on the "click to connect" goes to my launcher! Â And it works! Â Excellent!

Now I have one last thing I need to get working. Â If I click the 'Notepad 2016 - PLB' icon I get the regular Storefront ica file, so I don't get my LongCommandLine added into it. Â Can I change where it's trying to launch from?

Citrix appears to offer one extension that may allow this:

<img class="aligncenter size-full wp-image-2195" src="http://theorypc.ca/wp-content/uploads/2017/05/doLaunch.png" alt="" width="778" height="189" srcset="http://theorypc.ca/wp-content/uploads/2017/05/doLaunch.png 778w, http://theorypc.ca/wp-content/uploads/2017/05/doLaunch-300x73.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/doLaunch-768x187.png 768w" sizes="(max-width: 778px) 100vw, 778px" /> 

Huh. Â Well. Â That's not much documentation at all.

Fortunately, a [Citrix blog post came to rescue](https://www.citrix.com/blogs/2015/03/19/receiver-x1-apis/) with some more information:

<h2 style="padding-left: 30px;">
  Hook APIs That Allow Delays / Cancellations
</h2>

<p style="padding-left: 30px;">
  doLaunch<br /> doSubscribe<br /> doRemove<br /> doInstall
</p>

<p style="padding-left: 30px;">
  On each of these the customization might show a dialog, perform some checks (etc) but ultimately should call â€˜actionâ€™ if (and only if) they want the operation to proceed.
</p>

<div style="padding-left: 30px;">
  <pre courier="" 8221="" class="" style="padding-left: 30px;">CTXS.Extensions.doLaunch =Â Â function(app, action) { Â Â Â Â // call 'action' function if/when action should proceed Â Â Â Â action(); };</pre>
</div>

<p style="padding-left: 30px;">
  <p>
    This extension is taking an object (app). Â What properties does this object have? Â I did a simple console.log and examined the object:
  </p>
  
  <pre class="lang:default decode:true ">CTXS.Extensions.doLaunch =  function(app, action) {
    // call 'action' function if/when action should proceed
    console.log(app);
    action();
};</pre>
  
  <p>
    <img class="aligncenter size-full wp-image-2196" src="http://theorypc.ca/wp-content/uploads/2017/05/app.launchurl.png" alt="" width="1052" height="651" srcset="http://theorypc.ca/wp-content/uploads/2017/05/app.launchurl.png 1052w, http://theorypc.ca/wp-content/uploads/2017/05/app.launchurl-300x186.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/app.launchurl-768x475.png 768w" sizes="(max-width: 1052px) 100vw, 1052px" />
  </p>
  
  <p>
    Well, look that that. Â There is a property called 'launchurl'. Â Can we modify this property and have it point to our custom launcher?
  </p>
  
  <p>
    I modified my function as such:
  </p>
  
  <pre class="lang:default decode:true">CTXS.Extensions.doLaunch =  function(app, action) {
    // call 'action' function if/when action should proceed
	//modify launchurl to our PowerShell ICA creator
	app.launchurl = launchURL;
        console.log(app);
    action();
};</pre>
  
  <p>
    The result?
  </p>
  
  <p>
    <img class="aligncenter size-full wp-image-2197" src="http://theorypc.ca/wp-content/uploads/2017/05/modified_launch_url.png" alt="" width="1053" height="648" srcset="http://theorypc.ca/wp-content/uploads/2017/05/modified_launch_url.png 1053w, http://theorypc.ca/wp-content/uploads/2017/05/modified_launch_url-300x185.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/modified_launch_url-768x473.png 768w" sizes="(max-width: 1053px) 100vw, 1053px" />
  </p>
  
  <p>
    A modified launchurl!!!!
  </p>
  
  <p>
    Excellent!
  </p>
  
  <p>
    And launching it does return the ica file from my custom ica_launcher!
  </p>
  
  <p>
    Lastly, I want to autolaunch my program. Â It turns out, this is pretty simple. Â Just add the following the script.js file:
  </p>
  
  <pre class="lang:js decode:true ">//autolaunch application
CTXS.Extensions.noteApp = function(app) {
    if (app.encodedName.indexOf(CTX_Application) != -1) {
        CTXS.ExtensionAPI.launch(app);
    }
};</pre>
  
  <p>
    Beautiful. Â My full script.js file looks like so:
  </p>
  
  <pre class="lang:js decode:true">// Edit this file to add your customized JavaScript or load additional JavaScript files.

//grab the URL and parse out the parameters we want (application name, launch parameters)
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
var CTX_Application = getUrlParameter('CTX_Application');

console.log("NFuse_AppCommandLine " + NFuse_AppCommandLine);
console.log("CTX_Application " + CTX_Application);
	
	
CTXS.Extensions.excludeApp = function(app) {
    // return true or false if we don't match the target app name
	//we only want to show the application name found in the URI
	//if the app name does not equal the URI name then we hide it.
	if (app.name!= CTX_Application) {
		return true;
	}
};

//generated launch URL
var launchURL = "ica_launcher?CTX_Application=" + CTX_Application + "&NFuse_AppCommandLine=" + NFuse_AppCommandLine

CTXS.Extensions.doLaunch =  function(app, action) {
    // call 'action' function if/when action should proceed
	//modify launchurl to our PowerShell ICA creator
	app.launchurl = launchURL;
    action();
};

CTXS.Extensions.onAppHTMLGeneration = function(element) {
	//this function is not guaranteed to work across Storefront versions.  Ensure proper testing is conducted when upgrading.
	//tested on StoreFront 3.9
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\"><br></p>" );
	$( "div.storeapp-details-container" ).append( "<p class=\"storeapp-name\">If your application does not appear within a few seconds, <a href=\"" + launchURL + "\">click to connect</a></p>" );
};

//autolaunch application
CTXS.Extensions.noteApp = function(app) {
    if (app.encodedName.indexOf(CTX_Application) != -1) {
        CTXS.ExtensionAPI.launch(app);
    }
};
</pre>
  
  <p>
    And that's it. Â We are able to accomplish this with a Powershell script, and two customization files. Â I think this has a better chance of 'working' across Storefront versions then the SDK attempt I did earlier, or creating my own custom Storefront front end.
  </p>
  
  <!-- AddThis Advanced Settings generic via filter on the_content -->
  
  <!-- AddThis Share Buttons generic via filter on the_content -->