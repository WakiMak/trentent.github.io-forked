---
id: 2958
title: 'Citrix Logon Simulator&#8217;s &#8211; Part 2'
date: 2019-02-06T12:56:28-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2019/02/06/2923-revision-v1/
permalink: /2019/02/06/2923-revision-v1/
---
[In my previous post](https://theorypc.ca/2019/02/04/citrix-logon-simulators-part-1/) I was looking at utilizing a Logon Simulator to setup some proactive monitoring of a Citrix environment. Â I setup some goals for myself:

  1. Minimize the number of VM&#8217;s to run the robots
  2. As little resource consumption as possible
  3. Still provide operational alerts
  4. Operate on-premise

I want the footprint of these robots to be tiny. Â This must done on Server Core.

I want to run multiple instances of the logon simulator concurrently.

I need to be able to test &#8220;Stores&#8221; that do not have &#8220;Receiver for Web&#8221; sites enabled.

I want it so if I reboot the robot he picks up and starts running.

# The Choice.

In order to successfully hit these specified targets I opt&#8217;ed to use [ControlUp&#8217;s Logon Simulator](https://www.controlup.com/products/logon-simulator/). Â It can target the Store Service so it works with our &#8220;Receiver for Web&#8221;-less stores. Â It also has the features to generate events that can be targeted to send out notifications of an application launch failure.

# The Setup

In order to achieve my goals I need the following:

  * A Service Account that will be logging onto the Citrix servers
  * The robot (Windows 2019 Server Core)

I installed Server Core 2019 and added it to the domain.

## Configure Autologon

I configured group policy preferences to setup AutoLogon for my service account.Â This group policy object is set to the OU the robots reside in.

<div id="attachment_2924" style="width: 1373px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2924" class="wp-image-2924 size-full" src="http://theorypc.ca/wp-content/uploads/2019/01/GPP_AutoLogon.png" alt="" width="1363" height="414" srcset="http://theorypc.ca/wp-content/uploads/2019/01/GPP_AutoLogon.png 1363w, http://theorypc.ca/wp-content/uploads/2019/01/GPP_AutoLogon-300x91.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/GPP_AutoLogon-768x233.png 768w" sizes="(max-width: 1363px) 100vw, 1363px" /></p> 
  
  <p id="caption-attachment-2924" class="wp-caption-text">
    Group Policy Preferences settings to configure Autologon for our service account.
  </p>
</div>

However, I did not include the required &#8220;DefaultPassword&#8221; registry with the password.Â In order to embed the password in a more secure fashion, I had to manually use [Sysinternals AutoLogons](https://docs.microsoft.com/en-us/sysinternals/downloads/autologon).Â This keeps the password from being stored in plain text in the registry but this does need to be manually executed on each robot.

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2958-163" width="1140" height="912" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/01/AutoLogon.mp4?_=163" /><a href="http://theorypc.ca/wp-content/uploads/2019/01/AutoLogon.mp4">http://theorypc.ca/wp-content/uploads/2019/01/AutoLogon.mp4</a></video>
</div>

<p style="text-align: center;">
  <em>Configuring Autologon</em>
</p>

The account <span style="text-decoration: underline;"><strong>MUST</strong> </span>be a regular user and not a member of the &#8220;Administrators&#8221; group.Â This is a requirement of the ControlUp Logon Simulator.

## Prerequisites gotcha&#8217;s

Because I selected to use pure Server Core, there are some components that require fixing for full compatibility.Â This can actually be alleviated immediately by installing the [Feature On Demand (FoD) &#8220;Server App Compatibility&#8221;,](https://docs.microsoft.com/en-us/windows-server/get-started-19/install-fod-19)Â but this would increase both memory utilization and consume more disk space for our robot.Â However, if you prefer the easy way out, adding the FoD fixes everything and you can skip the &#8220;Fixes&#8221; section.Â Or just run the Logon Simulator on a operating system with the desktop experience.Â Otherwise, follow the steps in each of the solutions.

### Fixes

<span class="collapseomatic " id="id5d6e985ea30f8"  tabindex="0" title="Unable to install Citrix Reciever/Workspace App"    >Unable to install Citrix Reciever/Workspace App</span>

<div id="target-id5d6e985ea30f8" class="collapseomatic_content ">
  <div style="width: 1140px;" class="wp-video">
    <video class="wp-video-shortcode" id="video-2958-164" width="1140" height="912" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/01/CitrixWorkspaceAppError.mp4?_=164" /><a href="http://theorypc.ca/wp-content/uploads/2019/01/CitrixWorkspaceAppError.mp4">http://theorypc.ca/wp-content/uploads/2019/01/CitrixWorkspaceAppError.mp4</a></video>
  </div>
  
  <p>
    <img class="aligncenter size-full wp-image-2926" src="http://theorypc.ca/wp-content/uploads/2019/01/TrolleyError.png" alt="" width="420" height="162" srcset="http://theorypc.ca/wp-content/uploads/2019/01/TrolleyError.png 420w, http://theorypc.ca/wp-content/uploads/2019/01/TrolleyError-300x116.png 300w" sizes="(max-width: 420px) 100vw, 420px" />
  </p>
  
  <p>
    &#8220;TrolleyExpress.exe &#8211; System Error&#8221;<br /> The code execution cannot proceed because oledlg.dll was not found.Â Reinstalling the program may fix this problem.
  </p>
</div>

#### Solution

Copy oledlg.dll from the SysWow64 in either the install.wim or another &#8220;Windows Server 2019 (Desktop Experience)&#8221; install and put it in the C:\Windows\SysWow64 folder of your robot.

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2958-165" width="1140" height="912" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/01/RecieverInstall.mp4?_=165" /><a href="http://theorypc.ca/wp-content/uploads/2019/01/RecieverInstall.mp4">http://theorypc.ca/wp-content/uploads/2019/01/RecieverInstall.mp4</a></video>
</div>

&nbsp;

<span class="collapseomatic " id="id5d6e985ea448f"  tabindex="0" title="ControlUp Logon Simlulator is cropped on smaller displays"    >ControlUp Logon Simlulator is cropped on smaller displays</span>

<div id="target-id5d6e985ea448f" class="collapseomatic_content ">
  <p>
    <img class="aligncenter wp-image-2927 size-medium" src="http://theorypc.ca/wp-content/uploads/2019/01/croppedLogonSim-300x225.png" alt="" width="300" height="225" srcset="http://theorypc.ca/wp-content/uploads/2019/01/croppedLogonSim-300x225.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/croppedLogonSim-768x575.png 768w, http://theorypc.ca/wp-content/uploads/2019/01/croppedLogonSim.png 1023w" sizes="(max-width: 300px) 100vw, 300px" /> </div> 
    
    <h4>
      Solution
    </h4>
    
    <p>
      Set the resolution larger in your VM.
    </p>
    
    <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2958-166" width="1140" height="912" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/01/MissingSave.mp4?_=166" /><a href="http://theorypc.ca/wp-content/uploads/2019/01/MissingSave.mp4">http://theorypc.ca/wp-content/uploads/2019/01/MissingSave.mp4</a></video>
    </div>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      <span class="collapseomatic " id="id5d6e985ea4bb4"  tabindex="0" title="ControlUp Logon Simulator errors when you attempt to save the configuration"    >ControlUp Logon Simulator errors when you attempt to save the configuration</span>
    </p>
    
    <div id="target-id5d6e985ea4bb4" class="collapseomatic_content ">
      <div id="attachment_2928" style="width: 1267px" class="wp-caption aligncenter">
        <img aria-describedby="caption-attachment-2928" class="wp-image-2928 size-full" src="http://theorypc.ca/wp-content/uploads/2019/01/Error.png" alt="" width="1257" height="130" srcset="http://theorypc.ca/wp-content/uploads/2019/01/Error.png 1257w, http://theorypc.ca/wp-content/uploads/2019/01/Error-300x31.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/Error-768x79.png 768w" sizes="(max-width: 1257px) 100vw, 1257px" /></p> 
        
        <p id="caption-attachment-2928" class="wp-caption-text">
          &#8220;The logon simulator failed for the following reason: Creating an instance of the COM component with CLSID {C0B4E2F3-BA21-4773-8DBA-335EC946EB8B} from the IClassFactory failed due to the following error: 80040111 ClassFactory cannot supply requested class (Exception from HRESULT: 0x80040111 (CLASS_E_CLASSNOTAVAILABLE)).Â An application event log has been written to handle this crash.&#8221;
        </p>
      </div>
    </div>
    
    <h4>
      Solution
    </h4>
    
    <div style="width: 990px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2958-167" width="990" height="520" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/01/FixSaveError.mp4?_=167" /><a href="http://theorypc.ca/wp-content/uploads/2019/01/FixSaveError.mp4">http://theorypc.ca/wp-content/uploads/2019/01/FixSaveError.mp4</a></video>
    </div>
    
    <p>
      Copy ExplorerFrame.dll from the SysWow64 in either the install.wim or another &#8220;Windows Server 2019 (Desktop Experience)&#8221; install and put it in the C:\Windows\SysWow64 folder of your robot.Â Add the following registry:
    </p>
    
    <pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\WOW6432Node\CLSID\{AE054212-3535-4430-83ED-D501AA6680E6}]
@="Shell Name Space ListView"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\WOW6432Node\CLSID\{AE054212-3535-4430-83ED-D501AA6680E6}\InProcServer32]
@=hex(2):25,00,53,00,79,00,73,00,74,00,65,00,6d,00,52,00,6f,00,6f,00,74,00,25,\
  00,5c,00,73,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,65,00,78,00,\
  70,00,6c,00,6f,00,72,00,65,00,72,00,66,00,72,00,61,00,6d,00,65,00,2e,00,64,\
  00,6c,00,6c,00,00,00
"ThreadingModel"="Apartment"

</pre>
    
    <p>
      <span class="collapseomatic " id="id5d6e985ea4f35"  tabindex="0" title="ControlUp Logon Simulator detects admin rights"    >ControlUp Logon Simulator detects admin rights</span>
    </p>
    
    <div id="target-id5d6e985ea4f35" class="collapseomatic_content ">
      <div id="attachment_2929" style="width: 583px" class="wp-caption aligncenter">
        <img aria-describedby="caption-attachment-2929" class="wp-image-2929 size-full" src="http://theorypc.ca/wp-content/uploads/2019/01/AdminRights.png" alt="" width="573" height="133" srcset="http://theorypc.ca/wp-content/uploads/2019/01/AdminRights.png 573w, http://theorypc.ca/wp-content/uploads/2019/01/AdminRights-300x70.png 300w" sizes="(max-width: 573px) 100vw, 573px" /></p> 
        
        <p id="caption-attachment-2929" class="wp-caption-text">
          Admin rights detected The logon simulator should not be run as an administrator, please restart the app as a standard user.
        </p>
      </div>
    </div>
    
    <h4>
      Solution
    </h4>
    
    <p>
      Run the logon simulator as a standard user.
    </p>
    
    <hr />
    
    <h2>
      Configuration
    </h2>
    
    <p>
      Once you&#8217;ve implemented all the fixes, install Citrix Workspace App and ControlUp Logon Simulator with an account that is an administrator.
    </p>
    
    <p>
      Configure ControlUpLogonSim.Â With the simulator open, enter your Storefront details, ensuring to use the &#8220;Store&#8221; account as seen in the Storefront console.
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2941" src="http://theorypc.ca/wp-content/uploads/2019/01/CitrixStorefrontStore.png" alt="" width="1041" height="528" srcset="http://theorypc.ca/wp-content/uploads/2019/01/CitrixStorefrontStore.png 1041w, http://theorypc.ca/wp-content/uploads/2019/01/CitrixStorefrontStore-300x152.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/CitrixStorefrontStore-768x390.png 768w" sizes="(max-width: 1041px) 100vw, 1041px" />
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2942" src="http://theorypc.ca/wp-content/uploads/2019/01/LogonSimStoreFront.png" alt="" width="1103" height="235" srcset="http://theorypc.ca/wp-content/uploads/2019/01/LogonSimStoreFront.png 1103w, http://theorypc.ca/wp-content/uploads/2019/01/LogonSimStoreFront-300x64.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/LogonSimStoreFront-768x164.png 768w" sizes="(max-width: 1103px) 100vw, 1103px" />
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      For the &#8220;Resource to Launch&#8221; ensure the name matches the display name in Storefront:
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2947" src="http://theorypc.ca/wp-content/uploads/2019/01/ResourceSelection.png" alt="" width="1366" height="768" srcset="http://theorypc.ca/wp-content/uploads/2019/01/ResourceSelection.png 1366w, http://theorypc.ca/wp-content/uploads/2019/01/ResourceSelection-300x169.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/ResourceSelection-768x432.png 768w" sizes="(max-width: 1366px) 100vw, 1366px" />
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      In order to avoid <span style="text-decoration: underline;"><strong>session stealing</strong></span> in the simulator, each application will require a unique user name.Â Setup a unique account per application you are going to test.
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2952" src="http://theorypc.ca/wp-content/uploads/2019/02/LogonAccount.png" alt="" width="623" height="402" srcset="http://theorypc.ca/wp-content/uploads/2019/02/LogonAccount.png 623w, http://theorypc.ca/wp-content/uploads/2019/02/LogonAccount-300x194.png 300w" sizes="(max-width: 623px) 100vw, 623px" />
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      From here, enter your logon credentials for the account associated with the application.Â Run your first test by clicking the green triangle and ensure it works correctly.
    </p>
    
    <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2958-168" width="1140" height="641" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/02/SuccessfulLogonSim.mp4?_=168" /><a href="http://theorypc.ca/wp-content/uploads/2019/02/SuccessfulLogonSim.mp4">http://theorypc.ca/wp-content/uploads/2019/02/SuccessfulLogonSim.mp4</a></video>
    </div>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      Now that we have a successful run we set &#8220;Repeat Test&#8221; to ON and save the configuration.
    </p>
    
    <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2958-169" width="1140" height="641" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/02/SaveConfig.mp4?_=169" /><a href="http://theorypc.ca/wp-content/uploads/2019/02/SaveConfig.mp4">http://theorypc.ca/wp-content/uploads/2019/02/SaveConfig.mp4</a></video>
    </div>
    
    <p>
      I then created another application to monitor by renaming the &#8220;Resource to Launch&#8221; as another application and saved a second configuration.Â I saved all my files to a C:\Swinst folder.
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2950" src="http://theorypc.ca/wp-content/uploads/2019/02/2ndApp.png" alt="" width="1366" height="768" srcset="http://theorypc.ca/wp-content/uploads/2019/02/2ndApp.png 1366w, http://theorypc.ca/wp-content/uploads/2019/02/2ndApp-300x169.png 300w, http://theorypc.ca/wp-content/uploads/2019/02/2ndApp-768x432.png 768w" sizes="(max-width: 1366px) 100vw, 1366px" />
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      The point of all of this is to ensure the simulator is running in an automated fashion.Â To do so, we need to be able to configure the simulator to &#8220;launch&#8221; multiple different applications when the operating system starts.Â We have already configured autologon, we&#8217;ve setup our configuration files for each application we want to monitor, now we need to set the monitor to auto-start.
    </p>
    
    <p>
      Add the following registry key:
    </p>
    
    <pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run]
"ApplicationMonitor"="C:\\swinst\\StartAppMonitors.cmd"</pre>
    
    <p>
      And create a file &#8220;C:\Swinst\StartAppMonitors.cmd&#8221; with the following contents:
    </p>
    
    <pre class="lang:default decode:true ">START "" "C:\Program Files (x86)\ControlUp Logon Simulator\ControlUpLogonSim.exe" /s /noeula /config=C:\Swinst\Meditech.xml
START "" "C:\Program Files (x86)\ControlUp Logon Simulator\ControlUpLogonSim.exe" /s /noeula /config=C:\Swinst\Hyperspace_2014.xml</pre>
    
    <p>
      And watch the magic fly!
    </p>
    
    <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2958-170" width="1140" height="915" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2019/02/Completed.mp4?_=170" /><a href="http://theorypc.ca/wp-content/uploads/2019/02/Completed.mp4">http://theorypc.ca/wp-content/uploads/2019/02/Completed.mp4</a></video>
    </div>
    
    <p>
      And so the final question and the point of all this work, how much does this consume for our resources?
    </p>
    
    <p>
      &nbsp;
    </p>
    
    <p>
      <img class="aligncenter size-full wp-image-2954" src="http://theorypc.ca/wp-content/uploads/2019/02/ResourceConsumption.png" alt="" width="1463" height="640" srcset="http://theorypc.ca/wp-content/uploads/2019/02/ResourceConsumption.png 1463w, http://theorypc.ca/wp-content/uploads/2019/02/ResourceConsumption-300x131.png 300w, http://theorypc.ca/wp-content/uploads/2019/02/ResourceConsumption-768x336.png 768w" sizes="(max-width: 1463px) 100vw, 1463px" />
    </p>
    
    <p>
      1.1GB of RAM for the ENTIRE system, a peak CPU consumption of 7%, and the processes required to do the monitor use no CPU and only ~55MB of RAM.Â Each Citrix process consumes ~20MB of memory and is the most significant consumer of CPU but at the single digit % range.
    </p>
    
    <p>
      I anticipate doing some more stress testing to determine what the maximum amount of monitors I can get on one system, but I&#8217;m thrilled with these results.Â With this one box I would expect to be able to monitor dozens of application&#8230;Â Maybe a hundred?
    </p>
    
    <p>
      In the end, this was a fair bit of work to get this setup on Server Core, but I do believe the savings in resource consumption and overhead reduction will pay off.
    </p>
    
    <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->