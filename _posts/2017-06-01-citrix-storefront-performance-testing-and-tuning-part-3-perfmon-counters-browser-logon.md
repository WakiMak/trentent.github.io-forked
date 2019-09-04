---
id: 2380
title: Citrix Storefront â€“ Performance Testing and Tuning â€“ Part 3 â€“ PerfMon Counters, browser logon
date: 2017-06-01T14:42:02-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2380
permalink: /2017/06/01/citrix-storefront-performance-testing-and-tuning-part-3-perfmon-counters-browser-logon/
image: /wp-content/uploads/2017/06/storefront_icon22.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - PowerShell
  - scripting
  - Storefront
---
In looking at the performance of the Citrix Storefront Server, one of the thing I want is to understand what Storefront is doing at each stage of a session life cycle. Â There are two session-types that I&#8217;m curious about, a web browser based connection and a PNA connection. Â I&#8217;m going to examine a web browser based connection first.

Using my powershell script to simulate a user connection I put &#8216;pauses&#8217; between each stage. Â I then setup Perfmon to capture counters from the following objects:

<pre class="lang:default decode:true ">Citrix Dazzle Resources Controller\Image Response Whole Body  Calls / second
Citrix Dazzle Resources Controller\Image Response Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\List Sessions Whole Body  Calls / second
Citrix Dazzle Resources Controller\List Sessions Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\List Whole Body  Calls / second
Citrix Dazzle Resources Controller\List Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\ListWithoutAutoProvision Whole Body  Calls / second
Citrix Dazzle Resources Controller\ListWithoutAutoProvision Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\Update Resources Image Cache  Calls / second
Citrix Dazzle Resources Controller\Update Resources Image Cache Average Time (Microseconds)
Citrix Delegated Kerberos Authentication\Authenticate  Calls / second
Citrix Delegated Kerberos Authentication\Authenticate Average Time (Microseconds)
Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_agservices)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_agservices)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_configuration)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_configuration)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_roaming)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_roaming)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Receiver for Web\Get Ica file  Calls / second
Citrix Receiver for Web\Get Ica file Average Time (Microseconds)
Citrix Receiver for Web\Get icon  Calls / second
Citrix Receiver for Web\Get icon Average Time (Microseconds)
Citrix Receiver for Web\Get launch status  Calls / second
Citrix Receiver for Web\Get launch status Average Time (Microseconds)
Citrix Receiver for Web\List resources  Calls / second
Citrix Receiver for Web\List resources Average Time (Microseconds)
Citrix Receiver for Web\Login attempts  Calls / second
Citrix Receiver for Web\Login attempts Average Time (Microseconds)
Citrix Resources Common\Find all resources  Calls / second
Citrix Resources Common\Find all resources Average Time (Microseconds)
Citrix Resources Common\Find resource by id  Calls / second
Citrix Resources Common\Find resource by id Average Time (Microseconds)
Citrix Resources Common\ICA Launch  Calls / second
Citrix Resources Common\ICA Launch Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.11)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.12)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.12)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.13)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.14)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic  Calls / second
Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic  Calls / second
Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic Average Time (Microseconds)</pre>

I&#8217;ve added 6 farms to the storefront server to examine the load &#8220;in a real world&#8221; environment as Storefront does do some magic with concurrent enumeration. Â What I love that Citrix has done, is actually time the transactions AND gives you the &#8216;rate&#8217; the transactions are occurring at. Â This will make it much easier to baseline what your load is vs how I had to do it to measure the &#8216;rates&#8217; for Web Interface.

* * *

<span style="color: #ff0000;">$stage = &#8220;Initial Connection&#8221;</span>  
$store = <span style="color: #3366ff;">&#8220;http://storefront.bottheory.local/Citrix/StoreWeb/&#8221;</span>

So for a web browser based connection, I &#8216;connected&#8217; to the site and saw nothing. Â Storefront does very little work for the initial connection.

* * *

<span style="color: #ff0000;">$stage = &#8220;Client Configuration&#8221;</span>  
$store +Â <span style="color: #3366ff;">&#8220;Home/Configuration&#8221;</p> 

<p>
  Result:<br /> </span>
</p>

<pre class="lang:xhtml decode:true">&lt;clientSettings
	xmlns='http://citrix.com/delivery-services/webAPI/2-6/clientConfig'&gt;
	&lt;session timeout="600" /&gt;
	&lt;authManager getUsernameURL="Authentication/GetUserName" logoffURL="Authentication/Logoff" changeCredentialsURL="ExplicitAuth/GetChangeCredentialForm" loginFormTimeout="5" webviewReturnURL="ExplicitAuth/Bounce" webviewResumeURL="ExplicitAuth/ResumeForms" allowSelfServiceAccountManagementURL="ExplicitAuth/AllowSelfServiceAccountManagement" /&gt;
	&lt;storeProxy keepAliveURL="Home/KeepAlive"&gt;
		&lt;resourcesProxy listURL="Resources/List" resourceDetails="default" /&gt;
		&lt;sessionsProxy listAvailableURL="Sessions/ListAvailable" disconnectURL="Sessions/Disconnect" logoffURL="Sessions/Logoff" /&gt;
		&lt;clientAssistantProxy getDetectionTicketURL="ClientAssistant/GetDetectionTicket" getDetectionStatusURL="ClientAssistant/GetDetectionStatus" /&gt;
	&lt;/storeProxy&gt;
	&lt;pluginAssistant enabled="true" upgradeAtLogin="false" showAfterLogin="false"&gt;
		&lt;win32 path="http://downloadplugins.citrix.com/Windows/CitrixReceiverWeb.exe" /&gt;
		&lt;macOS path="http://downloadplugins.citrix.com/Mac/CitrixReceiverWeb.dmg" minimumSupportedOSVersion="10.6" /&gt;
		&lt;html5 enabled="Fallback" platforms="Firefox;Chrome;Version/([6-9]|\d\d).*Safari;MSIE \d\d;Trident/([6-9]|\d\d);Android;iPad;iPhone;iPod;" launchURL="clients/HTML5Client/src/SessionWindow.html" preferences="" singleTabLaunch="false" chromeAppOrigins="chrome-extension://haiffjcadagjlijoggckpgfnoeiflnem" chromeAppPreferences="" /&gt;
		&lt;protocolHandler enabled="true" platforms="(Macintosh|Windows NT).*((Firefox/((5[3-9]|[6789][0-9])|\d\d\d))|(Chrome/((4[2-9]|[56789][0-9])|\d\d\d)))(?!.*Edge)" skipDoubleHopCheckWhenDisabled="false" /&gt;
	&lt;/pluginAssistant&gt;
	&lt;userInterface autoLaunchDesktop="true" multiClickTimeout="3" enableAppsFolderView="true"&gt;
		&lt;workspaceControl enabled="true" autoReconnectAtLogon="true" logoffAction="disconnect" showReconnectButton="true" showDisconnectButton="true" /&gt;
		&lt;receiverConfiguration enabled="true" downloadURL="ServiceRecord/GetDocument/receiverconfig.cr" /&gt;
		&lt;uiViews showDesktopsView="true" showAppsView="true" defaultView="auto" /&gt;
		&lt;appShortcuts enabled="false" allowSessionReconnect="true" /&gt;
	&lt;/userInterface&gt;
	&lt;featureToggles&gt;
		&lt;feature name="Citrix.DeliveryServices.WingImpl.AlwaysOnTracing" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.DazzleResources.Web.AlwaysOnTracing" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.DazzleResourcesController.AnonymousPrelaunch" enabled="True" /&gt;
		&lt;feature name="Citrix.StoreFront.Roaming.NetScalerConfiguration" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.Admin.NetScalerImport" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.WebUI.ClientDetection.IfRequiredByApps" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.WebUI.WebviewCredentialSupport" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.ZonePreference" enabled="True" /&gt;
		&lt;feature name="Citrix.StoreFront.Authentication.Saml" enabled="True" /&gt;
		&lt;feature name="Citrix.StoreFront.AuthenticationToken.Jwt" enabled="False" /&gt;
		&lt;feature name="Citrix.StoreFront.Authentication.Cloud" enabled="False" /&gt;
		&lt;feature name="Citrix.StoreFront.XenMobile.SmartAccess" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.Authentication.SelfServiceAccountManagement" enabled="True" /&gt;
		&lt;feature name="Citrix.StoreFront.MultipleIISWebsites" enabled="True" /&gt;
		&lt;feature name="Citrix.DeliveryServices.HdxOverUdp" enabled="True" /&gt;
		&lt;feature name="Citrix.StoreFront.WebAndSaaSApps" enabled="False" /&gt;
		&lt;feature name="StoreFrontCloudAdmin" enabled="False" /&gt;
		&lt;feature name="StoreFrontCloudDeployment" enabled="False" /&gt;
		&lt;feature name="StoreFrontGWaaS_Product_RestrictPop" enabled="False" /&gt;
		&lt;feature name="StoreFrontMultiTenantBrokerService" enabled="False" /&gt;
	&lt;/featureToggles&gt;
&lt;/clientSettings&gt;</pre>

<p>
  ThisÂ request is to get the client configuration. Â Essentially, it pulls down an xmlÂ file from the store containing important links to various locations in Storefront. Again, no perf counter seems to occur and it doesn&#8217;t seem to generate any load.
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;Get Authentication Methods&#8221;</span><br /> $store + <span style="color: #3366ff;">&#8220;Resources/List&#8221;</span>
</p>

<p>
  &nbsp;
</p>

<pre class="lang:default decode:true">&lt;#
https://citrix.github.io/storefront-sdk/requests/#authentication-methods
Note
The client must first make a POST request to /Resources/List. Since the user is not yet authenticated, this returns a challenge in the form of a CitrixWebReceiver- Authenticate header with the GetAuthMethods URL in the location field.
#&gt;</pre>

<p>
  So this command starts to get a little exciting. Â It&#8217;s a bit of a misnomer to call it &#8220;Resources/List&#8221; as it doesn&#8217;t actually return resources, yet. Â It returns the challenge to logon. Â We get our first Peformance Counter hit.
</p>

<p>
  The fourÂ counters that register are
</p>

<p>
  &#8220;Citrix Receiver for Web\List resources Calls / second&#8221;<br /> &#8220;Citrix Receiver for Web\List resources Average Time (microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds)&#8221;<br /> Results:<br /> List resources Calls / second: 1<br /> List resources Average Time (microseconds):Â 3,714<br /> (dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds):Â 607<br /> (dazzleresources:list:get:_citrix_store)\Controller Action Calls / second:Â 1
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;Get Auth Methods&#8221;</span><br /> $store + <span style="color: #3366ff;">&#8220;Authentication/GetAuthMethods&#8221;</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<pre class="lang:xhtml decode:true">&lt;authMethods xmlns="http://citrix.com/delivery-services/webAPI/2-6/authMethods"&gt;

    &lt;method name="ExplicitForms" url="ExplicitAuth/Login"/&gt;

    &lt;method name="IntegratedWindows" url="DomainPassthroughAuth/Login"/&gt;

    &lt;method name="CitrixAuth" url="CitrixAuth/Login"/&gt;

&lt;/authMethods&gt;</pre>

<p>
  Another exciting call. Â This call hits these fourÂ performance counters:
</p>

<p>
  &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;
</p>

<p>
  Results:<br /> (protocols:choices:post:_citrix_authentication)\Controller Action Calls / second: 1<br /> (protocols:choices:post:_citrix_authentication)\Controller Action Average Time (Microseconds) : 488<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Calls / second: 1<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds): 138
</p>

<p>
  The Controller Action Calls /second was &#8220;1&#8221;, obviously I&#8217;m doing just a single test. Â For the action average time I got 711 microseconds. Â Extremely fast.
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;Domain Pass-Through and Smart Card Authentication&#8221;</span><br /> $store + <span style="color: #3366ff;">&#8220;DomainPassthroughAuth/Login&#8221;</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<pre class="lang:xhtml decode:true ">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;AuthenticationStatus xmlns="http://citrix.com/delivery-services/webAPI/2-6/authStatus"&gt;
  &lt;Result&gt;success&lt;/Result&gt;
  &lt;AuthType&gt;IntegratedWindows&lt;/AuthType&gt;
  
&lt;/AuthenticationStatus&gt;</pre>

<p>
  Two more perf counters hit (rate + time):
</p>

<p>
  Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Calls / second<br /> Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
</p>

<p>
  Results:<br /> (citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Calls / second: 1<br /> (citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Average Time (Microseconds): 4,850
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;Resource Enumeration&#8221;<br /> </span>$store + <span style="color: #3366ff;">&#8220;Resources/List&#8221;</span>
</p>

<p>
  <span style="color: #3366ff;">Response:<br /> </span>
</p>

<pre class="lang:js decode:true">{
	"isSubscriptionEnabled": true,
	"isUnauthenticatedStore": false,
	"resources": [
		{
			"clienttypes": [
				"ica30",
				"rdp"
			],
			"description": "MS Access 2010",
			"iconurl": "Resources/Icon/L0NpdHJpeC9TdG9yZS9yZXNvdXJjZXMvdjIvUVRkRE1EZzFORFUwTVVaRE5UZzJSVEkyTVVOQ09ETTFPRU5CUVRRMk5qUTFOakZCTmtZME1nLS0vaW1hZ2U-?size=128",
			"id": "CTX65.Office - Access 2010",
			"launchstatusurl": "Resources/GetLaunchStatus/QUhTQ1RYNjUuT2ZmaWNlIC0gQWNjZXNzIDIwMTAgLSBTb3V0aA--",
			"launchurl": "Resources/LaunchIca/QUhTQ1RYNjUuT2ZmaWNlIC0gQWNjZXNzIDIwMTAgLSBTb3V0aA--.ica",
			"name": "Access 2010",
			"path": "\\Office\\",
			"shortcutvalidationurl": "Resources/ValidateAppShortcutLaunch/QUhTQ1RYNjUuT2ZmaWNlIC0gQWNjZXNzIDIwMTAgLSBTb3V0aA--",
			"subscriptionurl": "Resources/Subscription/QUhTQ1RYNjUuT2ZmaWNlIC0gQWNjZXNzIDIwMTAgLSBTb3V0aA--"
		},
	]
}</pre>

<p>
  This call returns a list of all the resources for which you have access. Â The counters important to this call are:<br /> &#8220;Citrix Receiver for Web\List resources Average Time (microseconds)&#8221;<br /> &#8220;Citrix Dazzle Resources Controller\List Whole Body Calls / second&#8221;<br /> &#8220;Citrix Dazzle Resources Controller\List Whole Body Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Receiver for Web\List resourcesÂ Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.11)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.12)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.12)\Network Traffic Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic Calls / second&#8221;<br /> &#8220;Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic Average Time (Microseconds)&#8221;
</p>

<p>
  We have lots of exciting things happening here. Â We&#8217;re reaching back and querying the XML brokers, some kind of authentication occurs, etc.
</p>

<p>
  The results of these counters were:
</p>

<p>
  Citrix Receiver for Web\List resources Average Time (microseconds) : 249,172<br /> Citrix Dazzle Resources Controller\List Whole Body Calls / second : 1<br /> Citrix Dazzle Resources Controller\List Whole Body Average Time (Microseconds) : 182,206<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Calls / second : 1<br /> (dazzleresources:list:get:_citrix_store)\Controller Action Calls / second : 2<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds) : 4,549<br /> (dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds) : 8,391<br /> Citrix Receiver for Web\List resourcesÂ Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds) :Â 0<br /> Citrix Xml Service Communication(10.10.10.12)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.12)\Network Traffic Average Time (Microseconds) : 36,820<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds) : 37,622<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) : 43,121<br /> Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(xenapp5.bottheory.local)\Network Traffic Average Time (Microseconds) : 95,575<br /> Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(xenapp65t.bottheory.local)\Network Traffic Average Time (Microseconds) : 149,888
</p>

<p>
  With these counters we can see how long it took the brokers to respond. Â My broker that has a few hundred applications took the longest, followed by a broker of an older farm with dozens of apps, then three where only a single app each is published from them. Â I got a 0.0 for enumeration for one service. Â I&#8217;m not sure what that&#8217;s about (maybe it exceeded enumeration timeout and is now excluded?). Â Total time to list resources was 0.249 seconds.
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;Get User Name&#8221;</span><br /> $store + <span style="color: #3366ff;">&#8220;Authentication/GetUserName&#8221;</span>
</p>

<p>
  <span style="color: #3366ff;">Response:<br /> </span>
</p>

<pre class="lang:default decode:true ">Trentent Tye</pre>

<p>
  This call simply returns your display name (or logon name if no display name is specified).
</p>

<p>
  Two counters were hit:<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Â Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;
</p>

<p>
  The results of these counters were:<br /> (tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action Â Calls / second :Â 2<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Â Calls / second : 1<br /> (tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action Average Time (Microseconds) : 2,200<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds) : 4,551
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;AllowSelfServiceAccountManagement&#8221;</span><br /> $store + <span style="color: #3366ff;">&#8220;ExplicitAuth/AllowSelfServiceAccountManagement&#8221;</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<pre class="lang:xhtml decode:true ">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;SelfServiceAccountManagementStatus xmlns="http://citrix.com/delivery-services/webAPI/3-7/selfServiceAccountManagementStatus"&gt;
  &lt;AllowSelfServiceAccountManagement&gt;False&lt;/AllowSelfServiceAccountManagement&gt;
&lt;/SelfServiceAccountManagementStatus&gt;</pre>

<p>
  This call appears to return True or False.
</p>

<p>
  &#8220;Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Â Calls / second&#8221;<br /> &#8220;Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Â Calls / second&#8221;
</p>

<p>
  The results of these counters were:<br /> (selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Â Calls / second : 2<br /> (selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Average Time (Microseconds) : 2,247<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Â Calls / second : 1<br /> (authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds) : 6,014
</p>

<hr />

<p>
  &nbsp;
</p>

<p>
  <span style="color: #ff0000;">$stage = &#8220;Download all the icons&#8221;</span><br /> $store + <span style="color: #3366ff;">$iconurl</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<p>
  <downloads all icon files>
</p>

<p>
  This call hits the following counters:
</p>

<p>
  &#8220;Citrix Receiver for Web\Get icon Average Time (Microseconds)&#8221;<br /> &#8220;Citrix Receiver for Web\Get icon Calls / second&#8221;
</p>

<p>
  The results of these counters were:
</p>

<p>
  Citrix Receiver for Web\Get icon Average Time (Microseconds) : 285.351<br /> Citrix Receiver for Web\Get icon Calls / second : 152.259
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;ICA Launch.GetLaunchStatus&#8221;</span><br /> $store + <span style="color: #3366ff;">$appToLaunch.launchstatusurl</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<pre class="lang:js decode:true "> {"fileFetchUrl":null,"status":"success"}</pre>

<p>
  This call hits the following counters:
</p>

<p>
  Citrix Receiver for Web\Get launch status Calls / second<br /> Citrix Receiver for Web\Get launch status Average Time (Microseconds)<br /> Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Calls / second<br /> Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Average Time (Microseconds)<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Calls / second<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds)
</p>

<p>
  The results of these counters were:
</p>

<p>
  Citrix Receiver for Web\Get launch status Calls / second : 1<br /> Citrix Receiver for Web\Get launch status Average Time (Microseconds) : 152,721<br /> Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Calls / second : 1<br /> Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Average Time (Microseconds) : 5,091<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Calls / second: Â 2<br /> Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds) : 65,709
</p>

<p>
  Storefront will only fetch the status for the application from the broker that the application belongs. Â If I run this query multiple times, hitting different brokers, the XML service communication populates more information. Â The information here can be useful for you to find which broker is getting hit the mostÂ by looking at the &#8220;Network Traffic Calls / second&#8221; and you can find whether it&#8217;s overloaded by the &#8220;Network Traffic Average Time&#8221; being a number that grows as your load grows. Â As with everything, it&#8217;s probably best to get some &#8220;base&#8221; numbers when load is low and then again when load is high to get your range.
</p>

<hr />

<p>
  <span style="color: #ff0000;">$stage = &#8220;ICA Launch.LaunchIca&#8221;</span><br /> $store + <span style="color: #3366ff;">$appToLaunch.launchurl</span>
</p>

<p>
  <span style="color: #3366ff;">Response:</span>
</p>

<pre class="lang:default decode:true">[Encoding]
InputEncoding=UTF8

[WFClient]
ProxyFavorIEConnectionSetting=Yes
ProxyTimeout=30000
ProxyType=Auto
ProxyUseFQDN=Off
RemoveICAFile=yes
TransparentKeyPassthrough=Local
TransportReconnectEnabled=On
Version=2
VirtualCOMPortEmulation=On

[ApplicationServers]
Microsoft Office - Word=

[Microsoft Office - Word]
Address=10.1.1.89.52:1494
AutologonAllowed=On
BrowserProtocol=HTTPonTCP
CGPAddress=*:2598
ClientAudio=Off
DesiredColor=8
DesiredHRES=0
DesiredVRES=0
DoNotUseDefaultCSL=On
FontSmoothingType=0
HDXoverUDP=Off
InitialProgram=#Microsoft Office - Word
Launcher=WI
LaunchReference=JR4Y0nuCM8XcFYK9Vx5O+PO2dWcIJNM=
LocHttpBrowserAddress=!
LongCommandLine=
LPWD=234
NRWD=46
ProxyTimeout=30000
ProxyType=Auto
SessionsharingKey=-c17essqKrAujaDa2N
SFRAllowed=Off
SSLEnable=Off
startSCD=1496347847617
Title=Microsoft Office - Word
TransportDriver=TCP/IP
TRWD=0
TWIMode=On
UseLocalUserAndPassword=On
WinStationDriver=ICA 3.0

[Compress]
DriverNameWin16=pdcompw.dll
DriverNameWin32=pdcompn.dll

[EncRC5-0]
DriverNameWin16=pdc0w.dll
DriverNameWin32=pdc0n.dll

[EncRC5-128]
DriverNameWin16=pdc128w.dll
DriverNameWin32=pdc128n.dll

[EncRC5-40]
DriverNameWin16=pdc40w.dll
DriverNameWin32=pdc40n.dll

[EncRC5-56]
DriverNameWin16=pdc56w.dll
DriverNameWin32=pdc56n.dll
</pre>

<p>
  We get an ICA file. Â The performance counters that are important:
</p>

<p>
  Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Calls / second : 1<br /> Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Average Time (Microseconds) : 5,453<br /> Citrix Receiver for Web\Get Ica file Calls / second : 1<br /> Citrix Receiver for Web\Get Ica fileÂ Average Time (Microseconds) :Â 1,255,237<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second :Â 2<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) :Â 530,153
</p>

<p>
  &nbsp;
</p>

<hr />

<p>
  Final results:
</p>

<p>
  Taking all the operations and the time it took, I sorted the list according to duration.
</p>

<p>
  <img class="aligncenter size-full wp-image-2384" src="http://theorypc.ca/wp-content/uploads/2017/06/Storefront_operations.png" alt="" width="1210" height="567" srcset="http://theorypc.ca/wp-content/uploads/2017/06/Storefront_operations.png 1210w, http://theorypc.ca/wp-content/uploads/2017/06/Storefront_operations-300x141.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/Storefront_operations-768x360.png 768w" sizes="(max-width: 1210px) 100vw, 1210px" />
</p>

<p>
  The following operations list the total duration of that specific task:
</p>

<p>
  &#8220;Get Ica file&#8221;<br /> &#8220;List resources&#8221;<br /> &#8220;List Whole Body&#8221;<br /> &#8220;Get launch status&#8221;<br /> Excluding them shows us the sub-tasks that actually consumed the majority of the time spent:
</p>

<p>
  <img class="aligncenter size-full wp-image-2385" src="http://theorypc.ca/wp-content/uploads/2017/06/WithoutSortedCategories.png" alt="" width="1210" height="462" srcset="http://theorypc.ca/wp-content/uploads/2017/06/WithoutSortedCategories.png 1210w, http://theorypc.ca/wp-content/uploads/2017/06/WithoutSortedCategories-300x115.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/WithoutSortedCategories-768x293.png 768w" sizes="(max-width: 1210px) 100vw, 1210px" />
</p>

<p>
  The XML communications is what consumes the majority of the processing time. Â The items starting in &#8216;brackets ()&#8217; are the Citrix Storefront processing time that it spent. Â Being in the single &#8220;thousands&#8221; of microseconds is super fast. Â This essentially shows us that it takes next to no time at all for Storefront to execute the tasks that it needs.
</p>

<hr />

<p>
  Analysis:
</p>

<p>
  Storefront has some great new performance counters that can help you determine your pain points. Â Access to Citrix is usually talked about in terms of &#8216;rate&#8217; so the per-second and average time counters are excellent in helping determine what is the component taking so long to do whatever task. Â The breaking down of how longÂ <em>individual</em> XML brokers take is a <span style="text-decoration: underline;"><strong>HUGE</strong></span> plus as you should be able to accurately gauge which Citrix FARM is now causing slowness in logon, enumeration or launching for your users.
</p>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->