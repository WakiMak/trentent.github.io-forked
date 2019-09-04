---
id: 2263
title: Citrix Storefront â€“ Adventures in customization â€“ Dynamically configure features based on group membership
date: 2017-05-11T08:09:17-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2263
permalink: /2017/05/11/citrix-storefront-adventures-in-customization-dynamically-configure-features-based-on-group-membership/
image: /wp-content/uploads/2017/05/StoreFront_name.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
As per my previous posts, [dynamically configure Workspace Control](https://theorypc.ca/2017/05/07/citrix-storefront-adventures-in-customization-dynamically-configure-workspace-control-based-on-group-membership/) and [Authentication based on groupÂ membership](https://theorypc.ca/2017/05/10/citrix-storefront-adventures-in-customization-dynamically-configure-authentication-based-on-group-membership/), this post will put them all together and provide a little GUI love to enable showing your options. Â These two features need to be enabled and configured at different times. Â The authentication choice needs to be in placeÂ _before you logon._ Â Workspace Control is configuredÂ _upon logon._

In order to 'cheat', authentication actually uses Windows authentication from your browser to the server-side script. Â This way we can get your account and query your authentication feature group membership. Â This won't work with non-domain machines or browsers that don't support Windows authentication (Safari?). Â But this is OK. Â For those caveats, we just redirect those users to the explicit logon page anyways, since their user account won't be domain-aware.

If you get explicit logon, and then log in, we query the users entitlements for Workspace Control and configure accordingly. Â This allows a much more dynamic method than the authentication feature. Â By doing Workspace Control configuration after logonÂ _we can configure the settings to whichever user you use to logon._ Â So if my account is Explicit Logon, Workspace Control <span style="text-decoration: underline;">Disabled</span> that's what I get after the Storefront logon screen. Â However, if I logon with one of my test accounts that has Workspace Control <span style="text-decoration: underline;">Enabled</span> then it's enabled when I hit the app list. Â Even though my local user account and my Storefront logon are different I'll get the features of the user that logged in to Storefront. Â I think that's pretty damn neat.

Ok, so let's get to my finished product.

I edited the **custom\style.cssÂ** file and added the following:

<pre class="lang:css decode:true ">#customTop {
background:grey;
color: ghostwhite;
text-align: right;
font-size: 12px;
background-color: #968989;
}
</pre>

And my completed '**custom\script.js**' file:

<pre class="lang:js decode:true ">// Edit this file to add your customized JavaScript or load additional JavaScript files.

function getCookie(name) {
	var results = document.cookie.match('(^|;) ?' + name + '=([^;]*)');
	return results ? unescape(results[2]) : null;
}
  
/* 
============================================================================================
\\\\\\\\\\\\\\\\\Dynamic Authentication Based On Group Membership\\\\\\\\\\\\\\\\\\\\\\\\\\\
============================================================================================
*/
  
var url = "../../AHS-GroupMembership.aspx";
var displayedOptions = [];
ajaxWrapper({ url: url, type: "GET", dataType: 'text', async:false, success: explicitLogonCheck, error: explicitLogonCheck('Unknown') });

function checkDisplayedOptionsContent() {
	if (displayedOptions.length >= 1) {
		CTXS.trace ("Attempted authentication at least once. This usually completes on the second attempt. Removing first result...");
		displayedOptions.pop();
	}
}

function explicitLogonCheck(data) {
	
	switch(data) {
    case "PassThrough":
		checkDisplayedOptionsContent();
		displayedOptions.push("Pass-through");
        CTXS.trace ("PassThrough authentication detected");
		CTXS.Extensions.excludeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("excluding auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.EXPLICIT));
			return (authenticationMethod == CTXS.Authentication.Method.EXPLICIT);
		};
		CTXS.Extensions.includeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("including auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH));
			return (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH);
		};
        break;
    case "ExplicitLogon":
		checkDisplayedOptionsContent();
		displayedOptions.push("Explicit logon");
        CTXS.trace ("ExplicitLogon user group detected.");
		CTXS.Extensions.excludeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("excluding auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH));
			return (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH);
		};
		CTXS.Extensions.includeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("including auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.EXPLICIT));
			return (authenticationMethod == CTXS.Authentication.Method.EXPLICIT);
		};
        break;
    case "Unknown":
		displayedOptions.push("Defaulted to Explicit logon");
        CTXS.trace ("Attempting to authenticate.  If we cannot we will default to ExplicitLogon.");
		CTXS.Extensions.excludeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("excluding auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH));
			return (authenticationMethod == CTXS.Authentication.Method.PASSTHROUGH);
		};
		CTXS.Extensions.includeAuthenticationMethod = function(authenticationMethod) {
			CTXS.trace("including auth:" + authenticationMethod + ":" + (authenticationMethod == CTXS.Authentication.Method.EXPLICIT));
			return (authenticationMethod == CTXS.Authentication.Method.EXPLICIT);
		};
        break;
    default:
        CTXS.trace ("No possible authentication methods detected.");
	}
}
/* 
============================================================================================
/////////////////Dynamic Authentication Based On Group Membership///////////////////////////
============================================================================================
*/



/* 
============================================================================================
\\\\\\\\\\\\\\\\\\\\\\\\\\\Dynamic Workspace Control feature\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
============================================================================================
*/

function getUsername () {
	CTXS.trace("getUsername");
	// See https://citrix.github.io/storefront-sdk/requests/
	/*
	See Get User Name
	*/

	var usernameURL = CTXS.Config.getConfigValue("authManager.getUsernameURL");
	var result = "";
	ajaxWrapper({ type: "POST", async: false, url: usernameURL, dataType: "text", success: function (data) {
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
		url: "../../Get-GroupMembership?Displayname=" + username,
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
		displayedOptions.push("Session persistence enabled");
		CTXS.Extensions.showWebReconnectMenu = function () {return true};
		CTXS.Extensions.showWebDisconnectMenu = function () {return true};
		CTXS.Extensions.webReconnectAtStartup = function () {return true};
		CTXS.Extensions.webLogoffIcaAction = function () {return "disconnect"};
	} else {
		CTXS.trace("Disabling Workspace Control");
		displayedOptions.push("Session persistence disabled");
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
}
/* 
============================================================================================
///////////////////////////Dynamic Workspace Control feature////////////////////////////////
============================================================================================
*/

function ajaxWrapper(options) {
	var defaultOptions = {
		type: 'POST',
		dataType: 'json',
		traditional: true,
		beforeSend: function (jqXHR) {
			var csrfToken = getCookie('CsrfToken');
			if (csrfToken != null) {
				jqXHR.setRequestHeader("Csrf-Token", csrfToken);
			}
			var isUsingHttps = location.protocol.toLowerCase() == "https:" ? "Yes" : "No";
			jqXHR.setRequestHeader("X-Citrix-IsUsingHTTPS", isUsingHttps);
		},
		error: function () {
			CTXS.trace('Ajax error accessing URL: ' + options.url);
		}
	};

	options = $.extend({}, defaultOptions, options);
	$.ajax(options);
}

/* 
============================================================================================
                        Detected options feature bar
============================================================================================
*/
CTXS.Extensions.afterDisplayHomeScreen  = function () {
	CTXS.trace("afterDisplayHomeScreen stage");
	(function($) {
		var GUI = displayedOptions + "";
		$("#customTop").html("Detected: " + GUI + "&nbsp;&nbsp;&nbsp;");
	})(jQuery);
}

/* End of customization */
</pre>

You'll need to refer to my previous two posts for the Powershell HTTP LDAP listener and the Group-Membership.aspx file.

What does this look like for an end result?

<img class="aligncenter size-full wp-image-2267" src="http://theorypc.ca/wp-content/uploads/2017/05/Detected.png" alt="" width="892" height="253" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Detected.png 892w, http://theorypc.ca/wp-content/uploads/2017/05/Detected-300x85.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Detected-768x218.png 768w" sizes="(max-width: 892px) 100vw, 892px" /> 

&nbsp;

And if you are a member of the various combinations of groups?

<img class="aligncenter size-full wp-image-2265" src="http://theorypc.ca/wp-content/uploads/2017/05/Detected1.png" alt="" width="295" height="154" /> 

<img class="aligncenter size-full wp-image-2266" src="http://theorypc.ca/wp-content/uploads/2017/05/Detected2.png" alt="" width="295" height="148" /> 

<img class="aligncenter size-full wp-image-2264" src="http://theorypc.ca/wp-content/uploads/2017/05/Detected0.png" alt="" width="287" height="132" /> 

&nbsp;

I have to admit, this was a LOT easier than I thought it would be. Â Storefront is really powerful and easy to use. Â My biggest complaint is the documentation is lacking on how to use the API's but [searching the Citrix support forums proved very fruitful](http://discussions.citrix.com/index.php?search_term=CTXS&category=363&app=gsasearch&module=newsearch&do=newsearch&fromMainBar=1) (especially for how to use the authentication methods) for examples. Â Hopefully these blog articles will help someone else and further demonstrate the power, flexibility and extensibility of the Citrix Storefront product.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->