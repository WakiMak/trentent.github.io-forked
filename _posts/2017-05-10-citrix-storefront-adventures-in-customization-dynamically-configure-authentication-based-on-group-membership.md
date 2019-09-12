---
id: 2237
title: Citrix Storefront - Adventures in customization - Dynamically configure authentication based on group membership
date: 2017-05-10T13:31:04-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2237
permalink: /2017/05/10/citrix-storefront-adventures-in-customization-dynamically-configure-authentication-based-on-group-membership/
image: /wp-content/uploads/2017/05/featuredicon.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
Continuing on from my previous post, in XenApp 6.5 and Web Interface we created 2 sites, one with **explicit logon** (user name and password) set as the authentication method and one set with **domain passthrough**.  What was configured was an AD group, "ExplicitLogon", and then <span style="text-decoration: underline;">Windows Authentication</span> was configured on the <span style="text-decoration: underline;">IIS sites</span>.  When the user connected to the site, if they were a member of the domain, it would go to a custom aspx script that detected whether the user was a member of 'ExplicitLogon' or not.  If they were not a member they went directly to the domain passthrough site.  If they were a member, they would go to the explicit logon site and be prompted again for credentials.  Why is this configuration useful?  Two reasons...

  1. Allows lower level AD accounts to get an opportunity to logon to Citrix with accounts that have elevated permissions.  If your organization enforces 'non-admin' accounts for your local computers, then being a part of ExplicitLogon will direct you to the site where you can logon with an elevated account.
  2. Shared accounts that are not allowed access to Citrix applications.  If a shared account is detected as a member of ExplicitLogon it will give the _real_ user a logon prompt where they can logon with their own credentials.

This configuration has served us fairly well for the last few years, but it has a couple drawbacks.

  1. Non-domain joined machines get the ugly 'prompt' to enter some form of credentials so the group check can be executed where they will have to enter credentials again (if a member of ExplicitLogon).
  2. Non-domain joined machine may get an error after entering credentials in the initial prompt (if they are _NOT_ a member of ExplicitLogon).  The reason for this, that I can determine (and happens with non-domain joined Macintosh computers), is the browser will pass those AD credentials to the initial group membership check script but not to the next level - the domain passthrough check.

Can we solve the drawbacks with Storefront?

My thoughts are a new workflow that works like this:

1 - Attempt to query group membership with the browser's "user" _first  
_ 2- If we're unable to to find group membership -> proceed to ExplicitLogon page  
3 - If we retrieve group membership -> check for ExplicitLogon group  
4 - if 'ExplicitLogon' group == true -> proceed to ExplicitLogon page  
5 - if 'ExplicitLogon' group == false -> proceed with domain passthrough

This is the logical structure:

<img class="aligncenter size-full wp-image-2253" src="/wp-content/uploads/2017/05/ExplicitLogon_Flowchart.png" alt="" width="826" height="609" srcset="/wp-content/uploads/2017/05/ExplicitLogon_Flowchart.png 826w, /wp-content/uploads/2017/05/ExplicitLogon_Flowchart-300x221.png 300w, /wp-content/uploads/2017/05/ExplicitLogon_Flowchart-768x566.png 768w" sizes="(max-width: 826px) 100vw, 826px" /> 

&nbsp;

&nbsp;

Can I make this happen?  To start I created a store with both ExplicitLogon (User name and password) and Domain pass-through authentication methods enabled.

<img class="aligncenter size-full wp-image-2254" src="/wp-content/uploads/2017/05/auth_methods.png" alt="" width="552" height="425" srcset="/wp-content/uploads/2017/05/auth_methods.png 552w, /wp-content/uploads/2017/05/auth_methods-300x231.png 300w" sizes="(max-width: 552px) 100vw, 552px" /> 

The server side script to check group membership:

<pre class="lang:asp decode:true"><%
// Created by:		Saman Salehian
// Creation Date:	Dec 02, 2013
// Modified Date:	Apr 18, 2014
// File Name:		GroupMembership.aspx
// Description:		Redirect user to appropriate Citrix XenApp Web Site based on group membership
// Modified:            May 08, 2017 - Trentent Tye - Modified to check for group membership for ExplicitLogon
//                      and return the result.
%>

<%@ Page Language="C#" %>
<%@ Import Namespace="System.Security.Principal" %>
<%
{
System.Security.Principal.WindowsIdentity UserIdentity = (WindowsIdentity)Context.User.Identity;
System.Security.Principal.WindowsPrincipal UserPrincipal = new System.Security.Principal.WindowsPrincipal(UserIdentity);

string[] UserIdentityArr = UserIdentity.Name.Split('\\');
string UserDomainName = UserIdentityArr[0];



	if ((UserDomainName != null) && (UserDomainName.ToUpper()=="BOTTHEORY")) {
		if (UserPrincipal.IsInRole("BOTTHEORY\\ExplicitLogon")) {
			Response.Write("ExplicitLogon");
		} else {
			Response.Write("PassThrough");
		}
	} else {
	Response.Write("Unknown");
	}
}
%>
</pre>

And the technical flow:

<img class="aligncenter size-full wp-image-2255" src="/wp-content/uploads/2017/05/technical_flow_chart.png" alt="" width="814" height="1147" srcset="/wp-content/uploads/2017/05/technical_flow_chart.png 814w, /wp-content/uploads/2017/05/technical_flow_chart-213x300.png 213w, /wp-content/uploads/2017/05/technical_flow_chart-768x1082.png 768w" sizes="(max-width: 814px) 100vw, 814px" /> 

And this is what the **custom/script.js** looks like when we convert the flow to reality:

<pre class="lang:js decode:true">function getCookie(name) {
	var results = document.cookie.match('(^|;) ?' + name + '=([^;]*)');
	return results ? unescape(results[2]) : null;
}
  
var url = "../../GroupMembership.aspx";
ajaxWrapper({ url: url, type: "GET", dataType: 'text', async:false, success: explicitLogonCheck, error: explicitLogonCheck('Unknown') });

function explicitLogonCheck(data) {
	
	switch(data) {
    case "PassThrough":
        console.log ("PassThrough authentication detected");
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
        console.log ("ExplicitLogon user group detected.");
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
        console.log ("Attempting to authenticate.  If we cannot we will default to ExplicitLogon.");
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
        console.log ("No possible authentication methods detected.");
	}
}

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
			console.log('Ajax error accessing URL: ' + options.url);
		}
	};

	options = $.extend({}, defaultOptions, options);
	$.ajax(options);
}</pre>

&nbsp;

The results?

<img class="aligncenter size-full wp-image-2256" src="/wp-content/uploads/2017/05/auth_results.png" alt="" width="393" height="61" srcset="/wp-content/uploads/2017/05/auth_results.png 393w, /wp-content/uploads/2017/05/auth_results-300x47.png 300w" sizes="(max-width: 393px) 100vw, 393px" /> 

User: TEST1

<img class="aligncenter size-full wp-image-2258" src="/wp-content/uploads/2017/05/ExplicitLogon.png" alt="" width="617" height="380" srcset="/wp-content/uploads/2017/05/ExplicitLogon.png 617w, /wp-content/uploads/2017/05/ExplicitLogon-300x185.png 300w" sizes="(max-width: 617px) 100vw, 617px" /> 

User: TEST2

<img class="aligncenter size-full wp-image-2257" src="/wp-content/uploads/2017/05/Passthrough.png" alt="" width="846" height="421" srcset="/wp-content/uploads/2017/05/Passthrough.png 846w, /wp-content/uploads/2017/05/Passthrough-300x149.png 300w, /wp-content/uploads/2017/05/Passthrough-768x382.png 768w" sizes="(max-width: 846px) 100vw, 846px" /> 

&nbsp;

It works perfectly!  We can now define users to utilize ExplicitLogon via a user group.  And if they are not a member of that user group it will do pass-through authentication.  And even further to that, if they are NOT a member of the domain it will automatically redirect them to the Explicit Logon page!

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->