---
id: 2525
title: Citrix Storefront - Adventures in customization - Change any ICA parameter
date: 2017-07-31T17:04:07-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2525
permalink: /2017/07/31/citrix-storefront-adventures-in-customization-change-any-ica-parameter/
image: /wp-content/uploads/2017/07/Screen-Shot-2017-07-31-at-5.04.36-PM.png
categories:
  - Blog
tags:
  - Citrix
  - Storefront
---
When I was originally exploring [using the Citrix SDK to manipulate some ICA parameters](https://theorypc.ca/2017/04/24/citrix-storefront-experiences-with-storefront-customization-sdk-and-web-api), I found I couldn't do it.  Using Powershell and passing a header parameter I could manipulate the ICA file to how I wanted, but when I attempted to do so with Internet Explorer, I found it wasn't passing the header parameter.  I assumed this was because of how browsers were launching the ICA file.  However, I attempted to use Sam Jacob's [Citrix Storefront clientname manipulation extension](https://www.mycugc.org/blog/passing-parameters-to-&/xendesktop-by-rewriting-the-storefront-clientname), I discovered I had the same problem.  I originally thought I was doing something wrong, but on a whim I tried Chrome and it worked...  Perfectly!

When I investigated further [I found it appears that when Storefront detects IE it doesn't use the GetLaunchStatus API](https://theorypc.ca/2017/07/14/citrix-storefront-adventures-in-customization-customization-breaks-in-internet-explorer/), which passes the headers to Storefront for ICA manipulation.  I found a way to fix this was the add the IE detection to the script.js and add the missing call to the GetLaunchStatus API.

But now that I have headers being passed properly, I thought I should revisit my ICA parameter manipulation.  Sam's customization works great for manipulating the client name, but we have a few applications with various different requirements:

  1. Pass parameters to some specific applications
  2. Modify clientname based on the specific application that is launched

Can we replace Sam's customization with one that can manipulate any ICA parameter we so choose?

Yes.  Yes we can.  Storefront is very nice, flexible and powerful 🙂

I've designed this customization to look for an additional app setting tag.  This needs to be added to the web.config at the ""C:\inetpub\wwwroot\Citrix\Store\web.config"" level:

<pre class="lang:xhtml decode:true "><appSettings>
  <add key="modifyICAProperties" value="true" />
  </appSettings></pre>

Any parameter you want passed through needs to be enabled as a forwarded header in the ""C:\inetpub\wwwroot\Citrix\StoreWeb\web.config"" file:

<pre class="lang:xhtml decode:true"><communication attempts="1" timeout="00:03:00" loopback="Off"
          loopbackPortUsingHttp="80">
          <proxy enabled="true" processName="Fiddler" port="8888" />
          <forwardedHeaders>
			<header name="ClientName" />
			<header name="LongCommandLine" />
          </forwardedHeaders>
        </communication></pre>

&nbsp;

Lastly, you need to edit your \custom\script.js file to include your application and whatever parameters you want passed to it:

<pre class="lang:js decode:true ">CTXS.Extensions.doLaunch =  function(app, action) {
	//check for Notepad and configure clientname
	if (app.name == "Notepad - 2012") {
		$.ajaxSetup({
			headers : {
				'ClientName':'HappyHappyJoyJoy',
				'LongCommandLine':'"C:\\Windows\\DtcInstall.log"'
			}
		});
	}
	//fix for IE not executing GetLaunchStatus which is required to pass headers to our ICA file.
	if (CTXS.Device.isIE()) {
		app.launchstatusurl || (app.launchstatusurl = app.launchurl.replace("/LaunchIca/", "/GetLaunchStatus/").replace("/Launch/", "/GetLaunchStatus/"));
		var e = CTXS.ClientManager.getLaunchMethod() == CTXS.LaunchMethod.PROTOCOL_HANDLER;
		CTXS.ResourcesClient.getLaunchStatus(app, e)
	}
    action();
	delete $.ajaxSettings.headers["ClientName"]; //Remove header
	delete $.ajaxSettings.headers["LongCommandLine"]; //Remove header
};</pre>

One of the cool things about this is you can still use tags to change the behaviour of these applications.  For instance, if you want a certain subset of your applications to be 8bit you can do set check for that tag on app launch and add the [header to set the desired color](https://docs.citrix.com/content/dam/docs/en-us/receiver/windows/ica-settings/en.ica-settings.ica-settings-wrapper.pdf).  You can set certain applications to keep their ICA file by setting "RemoveICAFile" to Off.  You can individually modify any setting for a specific application or subset of applications.

Here is the [StoreCustomization_Launch.dll and source code](https://theorypc-my.sharepoint.com/personal/trententtye_theorypc_onmicrosoft_com/_layouts/15/guestaccess.aspx?docid=004f14ced27ca490cb0a405a05c20bd21&authkey=AbuSRo56HbF18BHjBi_Pyaw).

Here is the source code for prosperity:

<pre class="lang:c# decode:true ">/*************************************************************************
*
* Copyright (c) 2013-2015 Citrix Systems, Inc. All Rights Reserved.
* You may only reproduce, distribute, perform, display, or prepare derivative works of this file pursuant to a valid license from Citrix.
*
* THIS SAMPLE CODE IS PROVIDED BY CITRIX "AS IS" AND ANY EXPRESS OR IMPLIED
* WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
* MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
*
* Modified by Trentent Tye for ICA settings manipulation.
* 
* 
*************************************************************************/

using System.Configuration;
using System.Collections.Specialized;
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
            var icadetails = new IcaFile(valueToModify);
            Tracer.TraceInfo("Resource SDK: Launch customization point: ICA file modification");
            string modifyICAProperties = ConfigurationManager.AppSettings["modifyICAProperties"];
            if (modifyICAProperties == "true")
            {
                Tracer.TraceInfo("Modify ICA Properties");
                // ICA Settings were gotten from https://docs.citrix.com/content/dam/docs/en-us/receiver/windows/ica-settings/en.ica-settings.ica-settings-wrapper.pdf

                var Headers = context.HttpContext.Request.Headers;
                foreach (string header in Headers)
                {
                    switch (header)
                    {
                        case "AcceptURLType":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Address":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AECD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AllowAudioInput":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AllowVirtualDriverEx":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AllowVirtualDriverExLegacy":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AltProxyAutoConfigURL":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AltProxyBypassList":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AltProxyHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AltProxyPassword":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AltProxyType":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AlwaysSendPrintScreen":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AppendUsername":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioBandwidthLimit":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioDevice":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioDuringDetach":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioHWSection":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioInWakeOnInput":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AudioOutWakeOnOutput":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AUTHPassword":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AUTHUserName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "AutoLogonAllowed":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BrowserProtocol":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BrowserRetry":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BrowserTimeout":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BUCC":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BufferLength":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BufferLength2":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BypassSmartcardDomain":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BypassSmartcardPassword":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "BypassSmartcardUsername":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CbChainInterval":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CDMAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CDMReadOnly":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CFDCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CGPAddress":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CGPSecurityTicket":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ChannelName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ClearPassword":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ClientAudio":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ClientName":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ClipboardAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "COCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ColorMismatchPrompt_Have16_Want256":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ColorMismatchPrompt_Have16M_Want256":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ColorMismatchPrompt_Have64k_Want256":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "COMAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Command":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommandAckThresh":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommPollSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommPollWaitInc":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommPollWaitIncTime":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommPollWaitMax":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommPollWaitMin":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CommWakeOnInput":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ConnectionBar":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ConnectionFriendlyName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ContentRedirectionScheme":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ControlPollTime":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ConverterSection":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CPMAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRBrowserAcceptURLtype":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRBrowserCommand":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRBrowserPath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRBrowserPercentS":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRBrowserRejectURLtype":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CREnabled":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRPlayerAcceptURLtype":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRPlayerCommand":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRPlayerPath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRPlayerPercentS":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "CRPlayerRejectURLtype":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DataAckThresh":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DataBits":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DefaultHttpBrowserAddress":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DeferredUpdateMode":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DesiredColor":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DesiredHRES":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DesiredVRES":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DeviceName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DisableCtrlAltDel":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DisableDrives":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DisableMMMaximizeSupport":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DisableSound":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DisableUPDOptimizationFlag":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Domain":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DriverNameAlt":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DriverNameAltWin32":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DriverNameWin32":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DTR":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "DynamicCDM":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EmulateMiddleMouseButton":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EmulateMiddleMouseButtonDelay":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableAsyncWrites":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableAudioInput":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableClientSelectiveTrust":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableInputLanguageToggle":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableOSS":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableReadAhead":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableRtpAudio":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableSessionSharing":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableSessionSharingClient":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableSessionSharingHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EnableSSOThruICAFile":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "EncryptionLevelSession":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "endIFDCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "FONTSMOOTHINGTYPE":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ForceLVBMode":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "FriendlyName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "FullScreenBehindLocalTaskbar":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "FullScreenOnly":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HDXoverUDP":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey10Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey10Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey1Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey1Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey2Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey2Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey3Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey3Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey4Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey4Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey5Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey5Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey6Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey6Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey7Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey7Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey8Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey8Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey9Char":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HotKey9Shift":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HowManySkipRedrawPerPaletteChange":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "HttpBrowserAddress":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICAHttpBrowserAddress":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICAKeepAliveEnabled":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICAKeepAliveInterval":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICAPortNumber":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICAPrntScrnKey":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICASOCKSProtocolVersion":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICASOCKSProxyHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ICASOCKSProxyPortNumber":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "InitialProgram":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "InputEncoding":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "InstallColormap":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "IOBase":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "KeyboardLayout":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "KeyboardSendLocale":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "KeyboardTimer":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "KeyboardType":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Launcher":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LaunchReference":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LicenseType":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LocalIME":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LocHttpBrowserAddress":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LockdownProfiles":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogAppend":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogConfigurationAccess":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogConnect":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogErrors":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogEvidence":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogFile":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogFileGlobalPath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogFileWin32":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogFlush":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogonTicket":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LogonTicketType":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LongCommandLine":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Lpt1":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Lpt2":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Lpt3":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LPWD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "LvbMode2":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MaxDataBufferSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MaxMicBufferSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MaxOpenContext":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MaxPort":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MaxWindowSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MinimizeOwnedWindows":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MissedKeepaliveWarningMsg":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MissedKeepaliveWarningTime":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MouseTimer":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MouseWheelMapping":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "MSIEnabled":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NativeDriveMapping":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NDS":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NRUserName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NRWD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NumCommandBuffers":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "NumDataBuffers":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "OutBufCountClient":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "OutBufCountClient2":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "OutBufCountHost":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "OutBufCountHost2":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "OutBufLength":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PassThroughLogoff":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Password":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Path":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PCSCCodePage":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PCSCLibraryName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PercentS":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCacheEnabled":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCacheGlobalPath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCacheMinBitmap":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCachePath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCachePercent":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCacheSize":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PersistentCacheUsrRelPath":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PingCount":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PlaybackDelayThresh":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PNPDeviceAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "pnStartSCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Port1":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Port2":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "POSDeviceAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PrinterFlowControl":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PrinterResetTime":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PrinterThreadPriority":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "PrintMaxRetry":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyAuthenticationBasic":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyAuthenticationKerberos":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyAuthenticationNTLM":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyAuthenticationPrompt":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyAutoConfigURL":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyBypassList":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyFallback":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyFavorIEConnectionSetting":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyPassword":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyPort":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyTimeout":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyType":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyUseDefault":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyUseFQDN":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ProxyUsername":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ReadersStatusPollPeriod":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RECD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RegionIdentification":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RejectURLType":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RemoveICAFile":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ResMngrRunningPollPeriod":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "REWD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RtpAudioHighestPort":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "RtpAudioLowestPort":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ScalingHeight":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ScalingMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ScalingPercent":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ScalingWidth":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Schedule":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ScreenPercent":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SecureChannelProtocol":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SessionReliabilityTTL":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SessionSharingKey":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SessionSharingLaunchOnly":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SFRAllowed":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SkipRedrawPerPaletteChange":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SmartCardAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMA":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMAAudioEnabled":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMAMaxBufferThreshold":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMAMaximumBufferSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMAMinBufferThreshold":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMASecondsToBuffer":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SpeedScreenMMAVideoEnabled":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLCACert":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLCertificateRevocationCheckPolicy":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLCiphers":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLCommonName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLEnable":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSLProxyHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSOnCredentialType":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSOnDetected":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSOnUserSetting":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SSPIEnabled":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "startIFDCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "startSCD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "State":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SucConnTimeout":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "SwapButtons":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TransparentKeyPassthrough":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TransportReconnectDelay":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TransportReconnectEnabled":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TransportReconnectRetries":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TRWD":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Tw2CachePower":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TW2StopwatchMinimum":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TW2StopwatchScale":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TwainAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWIEmulateSystray":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWIFullScreenMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWIIgnoreWorkArea":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWIMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWISeamlessFlag":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWIShrinkWorkArea":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWISuppressZZEcho":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "TWITaskbarGroupingMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UnicodeEnabled":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UseAlternateAddress":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UseDefaultEncryption":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UseLocalUserAndPassword":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UseMRUBrowserPrefs":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Username":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UserOverride":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UsersShareIniFiles":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "UseSSPIOnly":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VariantName":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VirtualChannels":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VirtualCOMPortEmulation":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VirtualDriver":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VirtualDriverEx":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "VSLAllowed":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "Win32FavorRetainedPrinterSettings":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowManagerMoveIgnored":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowManagerMoveTimeout":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowsCache":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowSize":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowSize2":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WindowsPrinter":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WorkDirectory":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "WpadHost":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "XmlAddressResolutionType":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLAutoHiLimit":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLAutoLowLimit":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLDiskCacheSize":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLFntMemCacheSize":
                            icadetails.SetPropertyValue("WFClient", header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLKeyboardMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        case "ZLMouseMode":
                            icadetails.SetPropertyValue(IcaFile.ApplicationSection, header, GetHeaderValueFromContext(context, header));
                            break;
                        default:
                            Tracer.TraceInfo("Completed ICA file modification");
                            break;
                    }

                }
            }
            else
                Tracer.TraceError("Modify ICA Properties: No rule supplied");


            // get modified string back from helper breakdown class.
            string modifiedIcaFile = icadetails.ToString();

            //return the result
            return modifiedIcaFile;
        }

        // utility fn: return the set of values associated with a given header, in a single string joined by ';'s
        // returns null if header is not found in context
        private static string GetHeaderValueFromContext(CustomizationContextData context, string headerName)
        {
            NameValueCollection reqHeads = context.HttpContext.Request.Headers;
            string targetH = headerName.ToLower();
            string values = "";
            bool found = false;
            foreach (string hr in reqHeads.AllKeys)
            {
                if (hr.ToLower() == targetH)
                {
                    found = true;
                    int h = 0;
                    foreach (string hval in reqHeads.GetValues(hr))
                    {
                        if (h++ > 1)
                        {
                            values += ";";
                        }
                        values += hval;
                    }
                }
            }
            if (!found)
                return null;

            return values;
        }
        #endregion
    }
}</pre>

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->