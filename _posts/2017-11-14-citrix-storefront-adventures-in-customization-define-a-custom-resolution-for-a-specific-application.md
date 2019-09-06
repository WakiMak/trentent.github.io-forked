---
id: 2566
title: Citrix Storefront â€“ Adventures in customization â€“ Define a custom resolution for a specific application
date: 2017-11-14T14:33:13-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2566
permalink: /2017/11/14/citrix-storefront-adventures-in-customization-define-a-custom-resolution-for-a-specific-application/
image: /wp-content/uploads/2017/11/FeaturedImage.png
categories:
  - Blog
tags:
  - Citrix
  - scripting
  - Storefront

  - XenDesktop
---
Currently, Storefront does not grant the ability to define applications with specific resolutions.Â In order to configure the resolution, [Citrix recommends you modify the default.ica file](https://support.citrix.com/article/CTX116357).Â This is terrible!Â If you had specific applications that required specific resolutions, what are you to do?Â Direct users to a variety of stores depending on the resolution required?!

Fortunately, again, we can extend StoreFront to make it so we can configure custom resolutions for different applications on the same store.Â The [solution is a Storefront extension](https://theorypc.ca/2017/07/31/citrix-storefront-adventures-in-customization-change-any-ica-parameter/) I've already written.

The steps to set this up:

  1. Download the [Storefront_CustomizationLaunch.dll.](https://theorypc-my.sharepoint.com/personal/trententtye_theorypc_onmicrosoft_com/_layouts/15/guestaccess.aspx?docid=004f14ced27ca490cb0a405a05c20bd21&authkey=AbuSRo56HbF18BHjBi_Pyaw)
  2. Copy the file toÂ C:\inetpub\wwwroot\Citrix\Store\bin  
<img class="aligncenter size-full wp-image-2569" src="http://theorypc.ca/wp-content/uploads/2017/11/StoreCustomization_Launch.png" alt="" width="1112" height="489" srcset="http://theorypc.ca/wp-content/uploads/2017/11/StoreCustomization_Launch.png 1112w, http://theorypc.ca/wp-content/uploads/2017/11/StoreCustomization_Launch-300x132.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/StoreCustomization_Launch-768x338.png 768w" sizes="(max-width: 1112px) 100vw, 1112px" /> 
  3. Edit the web.config in theÂ **Store** directory and enable the extension
  4. <pre class="lang:xhtml decode:true"><appSettings>
  <add key="modifyICAProperties" value="true" />
  </appSettings>
</pre>

  5. We need to enable Header pass-through for DesiredHRES, DesiredVRES, and TWIMode in the "C:\inetpub\wwwroot\Citrix\StoreWeb\web.config" file:
  6. <pre class="lang:xhtml decode:true"><communication attempts="1" timeout="00:03:00" loopback="Off"
          loopbackPortUsingHttp="80">
          <proxy enabled="true" processName="Fiddler" port="8888" />
          <forwardedHeaders>
			<header name="DesiredHRES" />
			<header name="DesiredVRES" />
			<header name="TWIMode" />
          </forwardedHeaders>
        </communication></pre>

  7. Lastly, add the following to the custom.js file in your StoreWeb/custom folder: <pre class="lang:js decode:true ">CTXS.Extensions.doLaunch =  function(app, action) {
	//check for Calculator and configure custom resolution
	if (app.name == "Calculator - Mandatory") {
		$.ajaxSetup({
			headers : {
				'DesiredHRES':'800',
				'DesiredVRES':'600',
				'TWIMode':'Off',
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
	delete $.ajaxSettings.headers["DesiredHRES"]; //Remove header
	delete $.ajaxSettings.headers["DesiredVRES"]; //Remove header
	delete $.ajaxSettings.headers["TWIMode"]; //Remove header
};</pre>

  8. And enjoy the results!Â ðŸ™‚

<img class="aligncenter size-full wp-image-2570" src="http://theorypc.ca/wp-content/uploads/2017/11/800x600Calculator.png" alt="" width="802" height="632" srcset="http://theorypc.ca/wp-content/uploads/2017/11/800x600Calculator.png 802w, http://theorypc.ca/wp-content/uploads/2017/11/800x600Calculator-300x236.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/800x600Calculator-768x605.png 768w" sizes="(max-width: 802px) 100vw, 802px" /> 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->