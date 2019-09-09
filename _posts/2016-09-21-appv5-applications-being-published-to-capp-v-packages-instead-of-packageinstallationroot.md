---
id: 1725
title: AppV5 - Applications being published to "C:\App-V Packages" instead of %PACKAGEINSTALLATIONROOT%
date: 2016-09-21T10:13:06-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1725
permalink: /2016/09/21/appv5-applications-being-published-to-capp-v-packages-instead-of-packageinstallationroot/
enclosure:
  - |
    http://theorypc.ca/wp-content/uploads/2016/09/App-VPackages_popping_up.m4v
    198
    video/mp4
    
categories:
  - Blog
tags:
  - AppV
  - Group Policy
  - procmon
  - Registry
---
The last little while we've had an issue with some applications seemingly being published to "C:\App-V Packages" instead of our defined path "D:\AppVData\PackageInstallationRoot".

<div id="attachment_1728" style="width: 692px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1728" class="wp-image-1728 size-full" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.37.59-AM.png" alt="AppV-Packages" width="682" height="928" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.37.59-AM.png 682w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.37.59-AM-220x300.png 220w" sizes="(max-width: 682px) 100vw, 682px" /></p> 
  
  <p id="caption-attachment-1728" class="wp-caption-text">
    Notice the timestamps? Packages published during the same 'publishing refresh'
  </p>
</div>

&nbsp;

Video of the issue in action that I was able to setup a repro:

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1725-12" width="1140" height="845" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/09/App-VPackages_popping_up.m4v?_=12" /><a href="http://theorypc.ca/wp-content/uploads/2016/09/App-VPackages_popping_up.m4v">http://theorypc.ca/wp-content/uploads/2016/09/App-VPackages_popping_up.m4v</a></video>
</div>

So what's going on here that would cause this to happen?

I turned to the event viewer to find the time when this package "8FE4C99E..." was published and just looked to see if there was anything surrounding it.

<img class="aligncenter size-large wp-image-1729" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-1024x560.png" alt="Highlighted is the package publish event" width="1024" height="560" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-1024x560.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-300x164.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-768x420.png 768w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-550x300.png 550w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.10.41-AM-750x409.png 750w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

This is the event showing the package being published to "C:\App-V Packages".  There is an odd thing different about this publish event compared to the rest (that you can see above).  Between 'Configure Package' and when the 'Streaming' starts there is a 'Publishing Refresh' event.

<img class="aligncenter size-large wp-image-1730" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.11.03-AM-1024x394.png" alt="Publishing refresh" width="1024" height="394" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.11.03-AM-1024x394.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.11.03-AM-300x115.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.11.03-AM-768x295.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

Why is there a Publishing Refresh event?  It turns out, it can be triggered by a group policy refresh.

<img class="aligncenter size-full wp-image-1731" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.13.46-AM.png" alt="GP Refresh" width="868" height="1014" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.13.46-AM.png 868w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.13.46-AM-257x300.png 257w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.13.46-AM-768x897.png 768w" sizes="(max-width: 868px) 100vw, 868px" /> 

Notice the time stamps?

So, is there something that occurs during Group Policy refresh that could cause this?

To test this I used procmon to monitor for PackageInstallationRoot registry key and ran a Group Policy Refresh:

<img class="aligncenter size-full wp-image-1732" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.27.01-AM.png" alt="screen-shot-2016-09-21-at-8-27-01-am" width="837" height="366" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.27.01-AM.png 837w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.27.01-AM-300x131.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.27.01-AM-768x336.png 768w" sizes="(max-width: 837px) 100vw, 837px" /> 

<img class="aligncenter size-large wp-image-1733" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.35.29-AM-1024x248.png" alt="Procmon_PackageInstallationRoot" width="1024" height="248" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.35.29-AM-1024x248.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.35.29-AM-300x73.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.35.29-AM-768x186.png 768w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.35.29-AM.png 1348w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

The key is deleted then recreated and inbetween the AppV service is trying to read it.

So what is happening here?

At our organization we prefer to set Group Policies using Group Policies Preferences - Registry because of the power and flexibility of Item Level Targetting.  We prefer to only apply registry keys/policies to specific systems in certain groups rather than having a complicated OU structure.  Since I know we have a policy that configures our AppV5 values I went and looked into how this value was being configured:

<div id="attachment_1735" style="width: 1034px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1735" class="wp-image-1735 size-large" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.38.51-AM-1024x277.png" alt="GroupPolicyManagement" width="1024" height="277" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.38.51-AM-1024x277.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.38.51-AM-300x81.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.38.51-AM-768x207.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" /></p> 
  
  <p id="caption-attachment-1735" class="wp-caption-text">
    The 'Action' is 'Replace'
  </p>
</div>

<img class="aligncenter size-large wp-image-1737" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.18-AM-1024x406.png" alt="screen-shot-2016-09-21-at-8-44-18-am" width="1024" height="406" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.18-AM-1024x406.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.18-AM-300x119.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.18-AM-768x304.png 768w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.18-AM.png 1204w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

We have this value set to '[Replace](https://technet.microsoft.com/en-us/library/cc753092%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)'.  What does replace do?

<table summary="table">
  <tr>
    <td colspan="1">
      <strong>Replace</strong>
    </td>
    
    <td colspan="1">
      Delete and recreate a registry value or key for computers or users. If the target is a registry value, the net result of the <strong>Replace</strong> action is to overwrite all existing settings associated with the registry value. If the target is a registry key, the net result is to delete all values and subkeys in the key, leaving only a default value name with no data. If the registry value or key does not exist, then the <strong>Replace</strong> action creates a new registry value or key.
    </td>
  </tr>
  
  <tr>
    <td colspan="1">
    </td>
  </tr>
</table>

Well, process monitor is showing EXACTLY that scenario.  Replace is deleting the key and recreating it.  In between that time 'AppVClient.exe' is trying to read that value.  If PackageInstallationRoot doesn't exist, then AppV defaults to 'C:\App-V Packages" as the PackageInstallationRoot.

<div id="attachment_1736" style="width: 1034px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1736" class="wp-image-1736 size-large" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-10.01.23-AM-1024x470.png" alt="screen-shot-2016-09-21-at-10-01-23-am" width="1024" height="470" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-10.01.23-AM-1024x470.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-10.01.23-AM-300x138.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-10.01.23-AM-768x352.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" /></p> 
  
  <p id="caption-attachment-1736" class="wp-caption-text">
    No "PackageInstallationRoot" key -> "C:\App-V Packages" folder is where packages go.
  </p>
</div>

&nbsp;

It appears we have a race condition between group policy being applied and when our AppVClient is refreshing.  It is less than 600ms but that's enough time for a package or two to start their refresh and get caught in 'default folder' purgatory.

How can we fix this?

The easiest solution and the one we are going to implement:

We're going to change the action to "Update".  When I initially tried to change the action was greyed out.  When "Remove this item when it is no longer applied" is checked, it forces the action to "Replace".  
<img class="aligncenter size-large wp-image-1738" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.27-AM.png" alt="screen-shot-2016-09-21-at-8-44-27-am" width="402" height="446" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.27-AM.png 402w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.27-AM-270x300.png 270w" sizes="(max-width: 402px) 100vw, 402px" /> 

I had to 'uncheck' that value

<img class="aligncenter size-large wp-image-1739" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.34-AM.png" alt="screen-shot-2016-09-21-at-8-44-34-am" width="267" height="25" /> 

And then I could select 'Update'.

<img class="aligncenter size-large wp-image-1740" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.43-AM.png" alt="screen-shot-2016-09-21-at-8-44-43-am" width="401" height="448" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.43-AM.png 401w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-8.44.43-AM-269x300.png 269w" sizes="(max-width: 401px) 100vw, 401px" /> 

After updating the GPO and refreshing policies on the affected system, I ran procmon to verify that it does not delete the value anymore:

<img class="aligncenter size-large wp-image-1741" src="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.32.09-AM-1-1024x358.png" alt="GPP_Update" width="1024" height="358" srcset="http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.32.09-AM-1-1024x358.png 1024w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.32.09-AM-1-300x105.png 300w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.32.09-AM-1-768x268.png 768w, http://theorypc.ca/wp-content/uploads/2016/09/Screen-Shot-2016-09-21-at-9.32.09-AM-1.png 1354w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

It does not delete the value, it now 'Sets' the value to the same string we had before.  I don't know if the individual 'Set' action will cause an issue; I suspect not since the value will always exist and is technically the same value throughout the entire set of actions that this issue is now 'resolved' for us.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->