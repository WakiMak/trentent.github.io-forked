---
id: 2219
title: Citrix Storefront - Adventures in customization - Dynamically configure workspace control based on group membership
date: 2017-05-07T22:31:31-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2219
permalink: /2017/05/07/citrix-storefront-adventures-in-customization-dynamically-configure-workspace-control-based-on-group-membership/
image: /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-10.30.29-PM.png
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - Performance
  - Storefront
  - Web Interface

  - XenDesktop
---
I've been exploring the customization capabilities of Citrix Storefront and have some exciting ideas on simplifying our deployment.  What I'd really like is to reduce our store count down to a as few stores as possible.  In our Web Interface we have multiple stores based on the non-configurable settings.  They are:

  * Workspace Control Enabled with Explicit Logon
  * Workspace Control Disabled with Explicit Logon
  * Workspace Control Enabled with Domain Passthrough authentication
  * Workspace Control Disabled with Domain Passthrough authentication
  * Anonymous site

We can't mix and match authenticated sites and anonymous sites (right?... ?) but Citrix does offer the ability to configure Authentication methods AND Workspace Control options via their ['Receiver Extension API's'](https://docs.citrix.com/en-us/storefront/3/migrate-wi-to-storefront/receiver-extension-apis.html).

These are the API's in question:

```javascript
includeAuthenticationMethod(authenticationMethod)
excludeAuthenticationMethod(authenticationMethod)
showWebReconnectMenu(bool_showByDefault)
showWebDisconnectMenu(bool_showByDefault)
webReconnectAtStartup(bool_ReconnectByDefault)
webLogoffIcaAction(string_defaultAction
```


There isn't really a whole lot of documentation on them and how to use them.  Richard Hayton has created the [Citrix Customization Cookbook](https://www.citrix.com/blogs/2015/08/25/citrix-customization-cookbook/) which details some examples of some of the API's.  He has several [blog articles on the Citrix website](https://www.citrix.com/blogs/author/richardha/) that have varying degrees of applicability.  Unfortunately, he hasn't blogged in over a year on this topic as it _feels_ like the situation has changed a bit with the [Citrix Store API's available as well](https://citrix.github.io/storefront-sdk/requests/) (note: these are different!).

My target is to make it so these options can be set _**dynamically**_ based on the users group membership.  If you're a member of the group 'workspaceControlEnabled' you get all the settings set to true, if you're a member of 'workspaceControlDisabled' you get all the settings set to false.

Seems like a pretty straightforward goal?

So I thought I'd start with something pretty simple.  I have a store with Workspace Control Enabled, with show 'Connect and Disconnect' buttons selected:

<img class="aligncenter size-full wp-image-2220" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.15.34-PM.png" alt="" width="806" height="596" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.15.34-PM.png 806w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.15.34-PM-300x222.png 300w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.15.34-PM-768x568.png 768w" sizes="(max-width: 806px) 100vw, 806px" /> 

If I log into the site:

<img class="aligncenter size-full wp-image-2221" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.18.18-PM.png" alt="" width="212" height="313" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.18.18-PM.png 212w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.18.18-PM-203x300.png 203w" sizes="(max-width: 212px) 100vw, 212px" /> 

I see everything (as I should).

So let's start with a simple customization.  Let's try using the API to disable these options.  I created a totally blank script.js file and added the following lines:


```javascript
CTXS.Extensions.showWebReconnectMenu = function () {return false};
CTXS.Extensions.showWebDisconnectMenu = function () {return false};
CTXS.Extensions.webReconnectAtStartup = function () {return false};
CTXS.Extensions.webLogoffIcaAction = function () {return "disconnect"};
```

Now what does our menu look like?

<img class="aligncenter size-full wp-image-2222" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.22.05-PM.png" alt="" width="212" height="236" /> 

Awesome!  Workspace Control was disabled by script!

So I said I want to disable Workspace Control if you are a member of a specific group.  Richard Hayton actually wrote a pretty good article on [creating a service to facilitate grabbing your group membership with Storefront](https://www.citrix.com/blogs/2015/10/29/calling-your-own-services-from-receiver/).  Unfortunately, the download link is dead.  So I wrote another PowerShell HTTP listener that takes an input and queries AD for that user and their membership, and returns a positive value if workspace control should be enabled, or a negative value if it should be disabled.  To get it to query though, it needs a value.  The best value for this, I thought, would be the user name.

Citrix provides a function to get the username and I can then pass it to my webservice, test for group membership and return whether Workspace Control (WSC) should be enabled or disabled.

I wrote my first bit of code and it crashed immediately.  I simply entered it straight into my script.js.


```javascript
window.getCookieRegex = function(name) {
    match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
    if (match) return match[2];
  }

function getUsername () {
	CTXS.trace("getUsername");
	var usernameURL = CTXS.Config.getConfigValue("authManager.getUsernameURL");
	var CsrfToken = window.getCookieRegex("CsrfToken");
	var CtxAuthId = window.getCookieRegex("CtxsAuthId");
	var ASPNET_SessionId = window.getCookieRegex("ASP.NET_SessionId");
	
	$.ajax({
		headers: {
			'Csrf-Token':CsrfToken,
			'CtxAuthId':CtxAuthId,
			'ASP.NET_SessionId':ASPNET_SessionId,
			'X-Citrix-IsUsingHTTPS': 'No'
		},
		type: "POST",
		url: usernameURL,
		dataType: "text",
		success: function (data) {
			CTXS.trace("getUsername data:" + data);
			return data;
		}
	});
}

function workspaceControlEnabled(username) {
	CTXS.trace("workspaceControlEnabled");
		$.ajax({
		type: "GET",
		url: "../../Get-GroupMembership?Displayname=" + username,
		dataType: "text",
		success: function (data) {
			CTXS.trace("workspaceControlEnabled data:" + data);
			return data;
		}
	});
}

function setWorkspaceControl (bool) {
	CTXS.trace("setWorkspaceControl");
	if (!bool) {
		CTXS.Extensions.showWebReconnectMenu = function () {return false};
		CTXS.Extensions.showWebDisconnectMenu = function () {return false};
		CTXS.Extensions.webReconnectAtStartup = function () {return false};
		CTXS.Extensions.webLogoffIcaAction = function () {return "disconnect"};
	}
}
  
 var username = getUsername();
 var WSC = workspaceControlEnabled(username);
 setWorkspaceControl(WSC)
```


In order to trace the error, you simply enter "#-tr" to the end of your store URL:

<img class="aligncenter size-full wp-image-2223" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.44.15-PM.png" alt="" width="312" height="20" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.44.15-PM.png 312w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.44.15-PM-300x19.png 300w" sizes="(max-width: 312px) 100vw, 312px" /> 

and allow pop-ups.  A new tab will open allowing you to follow the 'flow' of Storefront as it executes its commands.  Mine crashed at:

<img class="aligncenter size-full wp-image-2224" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.49.01-PM.png" alt="" width="658" height="858" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.49.01-PM.png 658w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-1.49.01-PM-230x300.png 230w" sizes="(max-width: 658px) 100vw, 658px" /> 

"get username data:"

And it makes sense why it crashed there.  I haven't even logged in yet so it has no idea who or how to get the username so it looks like Storefront just returns the login page.  We need to call our functions **_after_** logging in.

Citrix provides the following event-based functions we can hook into:

<h2 style="padding-left: 30px;">
  Notifications of progress
</h2>

<h3 style="padding-left: 30px;">
  preInitialize(callback)<br /> postInitialize(callback)<br /> postConfigurationLoaded()<br /> postAppListLoaded()
</h3>

<p style="padding-left: 30px;">
  Note that during these calls, the UI may be hidden in native Receivers, so it is not safe to show UI<br /> For APIs passing a callback, you MUST call the callback function, though you may delay calling it until code of your own has run.
</p>

<h3 style="padding-left: 30px;">
  beforeLogon(callback)
</h3>

<p style="padding-left: 30px;">
  Web browsers only. Called prior to displaying any logon dialogs. You may call 'showMessage' here, or add your own UI.
</p>

<h3 style="padding-left: 30px;">
  beforeDisplayHomeScreen(callback)
</h3>

<p style="padding-left: 30px;">
  All clients, called prior to displaying the main UI. This is the ideal place to add custom startup UI.<br /> Note that for native clients, the user may not have logged in at this stage, as some clients allow offline access to the UI.
</p>

<h3 style="padding-left: 30px;">
  afterDisplayHomeScreen()
</h3>

<p style="padding-left: 30px;">
  All clients, called once the UI is loaded and displayed. The ideal place to call APIs to adjust the initial UI, for example to start in a different tab.
</p>

So the question becomes, when does each of these get called?  We are only interested in the ones **_after_** you login.  To determine this, I hooked into each one and just did a simple trace command and then refreshed my browser <span style="text-decoration: underline;">to the login screen</span>.


```javascript
CTXS.Extensions.preInitialize = function (callback) {
	CTXS.trace("preInitialize stage");
	callback()
}
CTXS.Extensions.postInitialize = function (callback) {
	CTXS.trace("postInitialize stage");
	callback()
}
CTXS.Extensions.postConfigurationLoaded = function () {
	CTXS.trace("postConfigurationLoaded stage");
}

CTXS.Extensions.postAppListLoaded = function () {
	CTXS.trace("postAppListLoaded stage");
}

CTXS.Extensions.beforeLogon  = function (callback) {
	CTXS.trace("beforeLogon stage");
	callback()
}

CTXS.Extensions.beforeDisplayHomeScreen = function (callback) {
	CTXS.trace("beforeDisplayHomeScreen stage");
	callback()
}

CTXS.Extensions.afterDisplayHomeScreen  = function () {
	CTXS.trace("afterDisplayHomeScreen stage");

```


This is where I stopped:

<img class="aligncenter size-full wp-image-2225" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-2.05.05-PM.png" alt="" width="916" height="296" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-2.05.05-PM.png 916w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-2.05.05-PM-300x97.png 300w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-2.05.05-PM-768x248.png 768w" sizes="(max-width: 916px) 100vw, 916px" /> 

And trace tab results:

<img class="aligncenter size-full wp-image-2226" src="/wp-content/uploads/2017/05/4stages.png" alt="" width="1092" height="506" srcset="/wp-content/uploads/2017/05/4stages.png 1092w, /wp-content/uploads/2017/05/4stages-300x139.png 300w, /wp-content/uploads/2017/05/4stages-768x356.png 768w" sizes="(max-width: 1092px) 100vw, 1092px" /> 

These 4 stages are called before the user logs on so they are of no use to me:

preInitialize  
postInitialize  
postConfigurationLoaded  
beforeLogon

The order of the other 3 after logon:

<img class="aligncenter size-large wp-image-2227" src="/wp-content/uploads/2017/05/final3stages-1600x668.png" alt="" width="1140" height="476" srcset="/wp-content/uploads/2017/05/final3stages-1600x668.png 1600w, /wp-content/uploads/2017/05/final3stages-300x125.png 300w, /wp-content/uploads/2017/05/final3stages-768x321.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

beforeDisplayHomeScreen  
postAppListLoaded  
afterDisplayHomeScreenStage

Before adding my code to the post-logon event functions I just added it back -plain jane- and rerun with a trace:

<img class="aligncenter size-large wp-image-2229" src="/wp-content/uploads/2017/05/WSC_Menu-1600x456.png" alt="" width="1140" height="325" srcset="/wp-content/uploads/2017/05/WSC_Menu-1600x456.png 1600w, /wp-content/uploads/2017/05/WSC_Menu-300x85.png 300w, /wp-content/uploads/2017/05/WSC_Menu-768x219.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

What I found is these extensions appear to have a fixed entry order point.  The workspace control extensions <span style="text-decoration: underline;"><strong>cannot</strong></span> be called _after_ "beforeDisplayHomeScreen" stage.  If you do not call the workspace control extensions before the callback on the 'beforeDisplayHomeScreen' function you will be unable to control the setting.  The trace log in my screenshot for these extensions will always occur at this point in time regardless if you actually set it in 'preInitialize, postInitialize, postConfigurationLoaded, or beforeLogon'.  And if you attempt to set it in either of the two later functions it will not log anything and your code has no effect.  So the only point in time where I can take the username and set these values are in the event function beforeDisplayHomeScreen.

_<Digress>_

_During the course of my testing this feature I had thought about adding a button that would allow you to toggle this feature 'enabled or disabled' on your own whim.  But it appears once you call the Extension it's a one and done.  I also discovered that it appears you must set the workspace control feature **early** in the process.  If I set it in postAppListLoaded or afterDisplayHomeScreen nothing happened.  To be fair, I do not know how to reinitialize the menu, maybe that would allow it to kick in dynamically...?  I guess that's for further exploration on another day..._

_</Digress>_

Ok, so we've found the one and only functional place we can execute our code.  So I added it.


```javascript
CTXS.Extensions.beforeDisplayHomeScreen = function (callback) {
	CTXS.trace("beforeDisplayHomeScreen stage");
	var username = getUsername();
	CTXS.trace("beforeDisplayHomeScreen username:" + username);
	var WSC = workspaceControlEnabled(username);
	CTXS.trace("beforeDisplayHomeScreen WSC:" + WSC);
	setWorkspaceControl(WSC);
	
	CTXS.trace("calling beforeDisplayHomeScreen callback");
	callback()

```


&nbsp;

The result?  Nothing.  Nothing happened.

Well, that's not _entirely_ true.

<img class="aligncenter size-large wp-image-2230" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-9.31.59-PM-1600x583.png" alt="" width="1140" height="415" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-9.31.59-PM-1600x583.png 1600w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-9.31.59-PM-300x109.png 300w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-07-at-9.31.59-PM-768x280.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

I've highlighted in yellow/orange my "getUsername" function.  We can see on <span style="text-decoration: underline;"><strong>line 35</strong></span> we get into the function.  And on <span style="text-decoration: underline;"><strong>line 67</strong></span> it is successfully finding and returning my name.  But the problem is that it's getting that information _after_ the point in time that we can set the WSC features (highlighted in blue - <span style="text-decoration: underline;"><strong>line 60-62</strong></span>).

I found that using ajax for this command and attempting to use async was causing my failure.  I understand it's bad practice to do synchronous commands, especially in javascript as they will lock the UI while executing, but, thus far it's the only way I know to ensure it gets completed **in the proper order.**  I am really not a web developer so I don't know what's the proper technique here to send a couple ajax requests that only blocks at the specific point in time that WSC kicks in...  Or find a way to redraw the menu?  But for the purposes of getting this working, this is the solution I've chosen to go with.  I'm wide open to better suggestions.  The real big extension that would be an issue is the 'webReconnectAtStartup'.  This feature will reconnect any existing sessions you have and the way that Citrix currently implements it, they want it run as soon as the UI is displayed.  This makes some sense as that's the whole point.  You don't want to wait around after logging in some indeterminate or random amount of seconds for your session to reconnect...  But, this issue can be alleviated.  _<span style="text-decoration: underline;">Citrix actually offers a way to implement this feature yourself via the Store API</span>_ so we could implement our own custom version of this function that would get all your sessions and reconnect them...

Which could just leave building the menu as something that could be moved back to async if I can figure out how to rebuild it or build it dynamically...

Anyway, that maybe for another day.  For today, the following works for my purpose.

This is my custom/script.js file that I finished from this blog post:


```javascript
window.getCookieRegex = function(name) {
    match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
    if (match) return match[2];
  }

function getUsername () {
	CTXS.trace("getUsername");
	// See https://citrix.github.io/storefront-sdk/requests/
	/*
	Get User Name
	Use this request to obtain the full user name, as configured in Active Directory. If the full user name is unavailable, the user's logon name is returned instead.

	Request
	URL	Method	Description
	/Authentication/ GetUserName	POST	Returns the user name. Obtain the URL from the Citrix Receiver for Web configuration using the getUsernameURL attribute at XPath /clientSettings/ authManager
	Notes

	This request requires an authenticated session, indicated by the cookies ASP.NET_SessionId and CtxsAuthId. When using an unauthenticated Store, no user has actually logged on and an HTTP 403 response is returned.
	The Web Proxy uses the StoreFront Token Validation service to obtain the user name from the authentication token.
	Response
	Response Code	Description
	200	Success or authentication challenge
	403	Forbidden, due to one of the following reasons:
	* Invalid CSRF token
	* Invalid X-Citrix-IsUsingHTTPS header w Invalid CtxsAuthId cookie
	* Missing authentication token in server session, when the user has not yet logged on
	Success Response Content
	If a successful response (HTTP status code 200 OK) is returned, the response body contains the user name as a string in plain text format, encoded as UTF-8.

	*/
	var usernameURL = CTXS.Config.getConfigValue("authManager.getUsernameURL");
	var CsrfToken = window.getCookieRegex("CsrfToken");
	var CtxAuthId = window.getCookieRegex("CtxsAuthId");
	var ASPNET_SessionId = window.getCookieRegex("ASP.NET_SessionId");
	var result = "";
	
	$.ajax({
		headers: {
			'Csrf-Token':CsrfToken,
			'CtxAuthId':CtxAuthId,
			'ASP.NET_SessionId':ASPNET_SessionId,
			'X-Citrix-IsUsingHTTPS': 'No'
		},
		type: "POST",
		async: false,
		url: usernameURL,
		dataType: "text",
		success: function (data) {
			CTXS.trace("getUsername data:" + data);
			result = data;
		}
	});
	return result;
}

function workspaceControlEnabled(username) {
	//call custom web service which queries LDAP for group membership and returns true for the "EnableWorkspaceControl" group, or false for the "DisableWorkspaceControl" group
	//if the user is not in either group the default is 'true'
	CTXS.trace("workspaceControlEnabled");
	var result = "";
	
		$.ajax({
		type: "GET",
		url: "../../Get-Groups?Displayname=" + username,
		dataType: "text",
		async: false,
		success: function (data) {
			CTXS.trace("workspaceControlEnabled data:" + data);
			result = data;
		}
	});
	return result;
}
	
function setWorkspaceControl (bool) {
	CTXS.trace("setWorkspaceControl:" + bool);
	if (bool == "true") {
		CTXS.trace("Enabling Workspace Control");
		CTXS.Extensions.showWebReconnectMenu = function () {return true};
		CTXS.Extensions.showWebDisconnectMenu = function () {return true};
		CTXS.Extensions.webReconnectAtStartup = function () {return true};
		CTXS.Extensions.webLogoffIcaAction = function () {return "disconnect"};
	} else {
		CTXS.trace("Disabling Workspace Control");
		CTXS.Extensions.showWebReconnectMenu = function () {return false};
		CTXS.Extensions.showWebDisconnectMenu = function () {return false};
		CTXS.Extensions.webReconnectAtStartup = function () {return false};
		CTXS.Extensions.webLogoffIcaAction = function () {return "disconnect"};
	}
}

CTXS.Extensions.beforeDisplayHomeScreen = function (callback) {
	CTXS.trace("beforeDisplayHomeScreen stage");
	var username = getUsername();
	var WSC = workspaceControlEnabled(username);
	setWorkspaceControl(WSC);
	
	CTXS.trace("calling beforeDisplayHomeScreen callback");
	callback()

```


&nbsp;

Here is the LDAP_HttpListener.psm1


```powershell
# Copyright (c) 2014 Microsoft Corp.
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

Function Get-GroupMembership {
    <#
    .Synopsis
        Queries Active Directory for a group and returns true or false
    .Description
        This function takes a single parameter "Display Name" which you can retrieve from StoreFront and pass to this function.
        It will lookup the user and the groups they belong to and determine if they are a member of said group.  This function
        does NOT search nested groups as there is a higher cost associated with that search.
    .Parameter DisplayName
        AD attribute "displayName".  eg, "Trentent Tye" or "Trentent Tye - Admin"
    .Example
        Get-GroupMembership -displayName "Trentent Tye"
    #>
    Param (
    [Parameter()]
    [String] $DisplayName = ""
    )

    Process {

        Write-Verbose "Get-GroupMembership"

        $Searcher = New-Object DirectoryServices.DirectorySearcher
        $Searcher.Filter = '(&(objectClass=user)(displayName=' + $DisplayName +'))'
        $Searcher.SearchRoot = 'LDAP://OU=Accounts,DC=bottheory,DC=local'

        if ($Searcher.FindAll().Properties.memberof -match "WorkspaceControlDisabled") {
            return "false"
        } else {
            return "true"
        }
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
        Start-HTTPListener -Port 8080 -Url PowerShell
        Invoke-WebRequest -Uri "http://localhost:8888/PowerShell?command=get-service winmgmt&format=text" -UseDefaultCredentials | Format-List *
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
                    $displayName = $request.QueryString.Item("displayName")

                    #uncomment next portion to allow remote exit of the listener
                    <#
                    if ($displayName -eq "exit") {
                        Write-Verbose "Received command to exit listener"
                        return
                    }
                    #>

                    $Format = $request.QueryString.Item("format")
                    if ($Format -eq $Null) {
                        $Format = "JSON"
                    }

                    Write-Verbose "displayName = $displayName"
                    Write-Verbose "Format = $Format"


                    Write-Verbose "Executing Command"
                    ## execute command here...
                    $script = Get-GroupMembership -displayName $displayName
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

```


Lastly, the scheduled task to call the listener:


```powershell
ipmo ".\HTTPListener.psm1"
#Example URL
#http://bottheory.local/Get-Groups?DisplayName=Trentent Tye
Start-HTTPListener -Port 80 -Url "Get-Groups" -Auth Anonymous
```


<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->