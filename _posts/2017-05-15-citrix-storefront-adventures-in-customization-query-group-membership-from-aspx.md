---
id: 2275
title: Citrix Storefront - Adventures in customization - Query group membership from aspx
date: 2017-05-15T08:19:41-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2275
permalink: /2017/05/15/citrix-storefront-adventures-in-customization-query-group-membership-from-aspx/
image: /wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.40.36-PM.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront
---
I've written a script that can tie into your environment for Storefront or other web service.  This is preferable over the powershell HTTP listener (IMHO) because it can just run on the IIS server and doesn't need to rely on any external program/service.  It's a simple script to pull out whether a user is a part of a group or not.  However, it does require impersonation to be able query Active Directory if your environment does not allow anonymous queries (I believe most do not).  Impersonation will make the request come from the machine account, which typically does have authorization to query AD.

In order to set this up, I've created a web application in IIS with impersonation set.


```batch
cd "%WINDIR%\system32\inetsrv"

:: Create application pool 
appcmd add apppool /Name:"Impersonate"
appcmd set config /section:applicationPools -[name='Impersonate'].processModel.identityType:NetworkService

:: add web app
mkdir C:\inetpub\wwwroot\ADInfo
appcmd add app /site.name:"Default Web Site" /path:/ADInfo /physicalPath:C:\inetpub\wwwroot\ADInfo
appcmd set app "Default Web Site/ADInfo" -applicationPool:Impersonate
appcmd set config "Default Web Site/ADInfo" /section:system.web/compilation /+"assemblies.[assembly='System.DirectoryServices, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a']"
```

NOTE: this was run on a Server 2016 box.  If your System.DirectoryServices assembly has a different version and public key you will need to update this script with that information.

This script does the following: "Create application pool"

<img class="aligncenter size-full wp-image-2276" src="/wp-content/uploads/2017/05/App_Pools.png" alt="" width="809" height="377" srcset="/wp-content/uploads/2017/05/App_Pools.png 809w, /wp-content/uploads/2017/05/App_Pools-300x140.png 300w, /wp-content/uploads/2017/05/App_Pools-768x358.png 768w" sizes="(max-width: 809px) 100vw, 809px" />  
<img class="aligncenter size-full wp-image-2277" src="/wp-content/uploads/2017/05/AppPool_AdvancedSettings.png" alt="" width="439" height="545" srcset="/wp-content/uploads/2017/05/AppPool_AdvancedSettings.png 439w, /wp-content/uploads/2017/05/AppPool_AdvancedSettings-242x300.png 242w" sizes="(max-width: 439px) 100vw, 439px" /> 

&nbsp;

"Add Web App"

<img class="aligncenter size-full wp-image-2279" src="/wp-content/uploads/2017/05/WebApp.png" alt="" width="603" height="247" srcset="/wp-content/uploads/2017/05/WebApp.png 603w, /wp-content/uploads/2017/05/WebApp-300x123.png 300w" sizes="(max-width: 603px) 100vw, 603px" /> 

And the "appcmd set config" creates our web.config file:

<img class="aligncenter size-full wp-image-2280" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.32.48-PM.png" alt="" width="1126" height="634" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.32.48-PM.png 1126w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.32.48-PM-300x169.png 300w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.32.48-PM-768x432.png 768w" sizes="(max-width: 1126px) 100vw, 1126px" /> 

And the GroupMembership.aspx file:

```jsp
<%
// Created by:		Trentent Tye
// Creation Date:	May 11, 2017
// File Name:		GroupMembership.aspx
// Description:		Checks for specific group membership and returns true or false
%>

<%@ Page Language="C#" %>
<%@ Import Namespace="System.DirectoryServices" %>
<%
{
string parameter = Request.QueryString["DisplayName"];

DirectorySearcher ds = new DirectorySearcher("LDAP://OU=Accounts,DC=bottheory,DC=local");  // must be in the format LDAP://OU=Accounts,DC=bottheory,DC=local
ds.Filter = string.Format("(&(objectCategory=person)(objectClass=user)(displayName={0}))", parameter);

SearchResult sr;
sr = ds.FindOne();  
if (sr == null) {
	//try searching with SamAccountName as Citrix will default to that if the DisplayName isn't populated.
	// https://citrix.github.io/storefront-sdk/requests/#get-user-name
	// "If the full user name is unavailable, the user's logon name is returned instead."
	ds.Filter = string.Format("(&(objectCategory=person)(objectClass=user)(samAccountName={0}))", parameter);
	sr = ds.FindOne(); 
}

try
	{
		var memberGroups = sr.Properties["memberOf"];

		string Ret = "true";
		foreach(object memberOf in memberGroups)
		{
		if ((memberOf.ToString()).Contains("SessionPersistenceDisabled"))
			{
				Ret = "false";
			}
		}
		Response.Write(Ret);
	}
		catch (Exception ex)
	{
		Response.Write("NotFound");
	}
}
%>
```

To call the file, it's the %hostname%\ADInfo\GroupMembership.aspx?DisplayName=%username%

For example:

<img class="aligncenter size-full wp-image-2281" src="/wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.36.38-PM.png" alt="" width="607" height="67" srcset="/wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.36.38-PM.png 607w, /wp-content/uploads/2017/05/Screen-Shot-2017-05-13-at-2.36.38-PM-300x33.png 300w" sizes="(max-width: 607px) 100vw, 607px" /> 

&nbsp;

And then we just modify our script.js to point to this URL instead:


```javascript
function workspaceControlEnabled(username) {
	//call custom web service which queries LDAP for group membership and returns true for the "EnableWorkspaceControl" group, or false for the "DisableWorkspaceControl" group
	//if the user is not in either group the default is 'true'
	CTXS.trace("workspaceControlEnabled");
	var result = "";
	
		$.ajax({
		type: "GET",
		url: "../../ADInfo/GroupMembership.aspx?Displayname=" + username,
		dataType: "text",
		async: false,
		success: function (data) {
			CTXS.trace("workspaceControlEnabled data:" + data);
			result = data;
		}
	});
	return result;
```

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->