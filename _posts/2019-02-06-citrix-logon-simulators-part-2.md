---
id: 2917
title: Citrix Logon Simulator's - Part 2
date: 2019-02-06T11:00:26-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2916
permalink: /2019/02/06/citrix-logon-simulators-part-2/
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - Logons
  - Performance
  - Storefront

  - XenDesktop
---
[In my previous post](/2019/02/04/citrix-logon-simulators-part-1/) I was looking at utilizing a Logon Simulator to setup some proactive monitoring of a Citrix environment.  I setup some goals for myself:

1. Minimize the number of VM’s to run the robots
2. As little resource consumption as possible
3. Still provide operational alerts
4. Operate on-premise
I want the footprint of these robots to be tiny.  This must done on Server Core.

I want to run multiple instances of the logon simulator concurrently.

I need to be able to test “Stores” that do not have “Receiver for Web” sites enabled.

I want it so if I reboot the robot he picks up and starts running.

# The Choice.

In order to successfully hit these specified targets I opt’ed to use ControlUp’s Logon Simulator.  It can target the Store Service so it works with our “Receiver for Web”-less stores.  It also has the features to generate events that can be targeted to send out notifications of an application launch failure.

# The Setup

In order to achieve my goals I need the following:

* A Service Account that will be logging onto the Citrix servers
* The robot (Windows 2019 Server Core)
I installed Server Core 2019 and added it to the domain.

## Configure Autologon

I configured group policy preferences to setup AutoLogon for my service account.  This group policy object is set to the OU the robots reside in.

<figure class="wp-block-image"><img src="/wp-content/uploads/2019/01/GPP_AutoLogon.png"/><figcaption>Group Policy Preferences settings to configure Autologon for our service account.</figcaption></figure>

However, I did not include the required “DefaultPassword” registry with the password.  In order to embed the password in a more secure fashion, I had to manually use [Sysinternals AutoLogons](https://docs.microsoft.com/en-us/sysinternals/downloads/autologon).  This keeps the password from being stored in plain text in the registry but this does need to be manually executed on each robot.

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/01/AutoLogon.mp4"></video><figcaption>Direct Connection Process</figcaption></figure> 

The account **MUST** be a regular user and not a member of the “Administrators” group.  This is a requirement of the ControlUp Logon Simulator.

## Prerequisites gotcha’s
Because I selected to use pure Server Core, there are some components that require fixing for full compatibility.  This can actually be alleviated immediately by installing the [Feature On Demand (FoD) “Server App Compatibility”](https://docs.microsoft.com/en-us/windows-server/get-started-19/install-fod-19), but this would increase both memory utilization and consume more disk space for our robot.  However, if you prefer the easy way out, adding the FoD fixes everything and you can skip the “Fixes” section.  Or just run the Logon Simulator on a operating system with the desktop experience.  Otherwise, follow the steps in each of the solutions.

### Fix 1
Unable to install Citrix Reciever/Workspace App

#### Solution 1
Copy oledlg.dll from the SysWow64 in either the install.wim or another “Windows Server 2019 (Desktop Experience)” install and put it in the C:\Windows\SysWow64 folder of your robot.

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/01/RecieverInstall.mp4"></video></figure> 

### Fix 2 
ControlUp Logon Simlulator is cropped on smaller displays

#### Solution 2
Set the resolution larger in your VM.

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/01/MissingSave.mp4"></video></figure> 

### Fix 3
ControlUp Logon Simulator errors when you attempt to save the configuration

#### Solution 3
<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/01/FixSaveError.mp4"></video></figure> 
Copy ExplorerFrame.dll from the SysWow64 in either the install.wim or another “Windows Server 2019 (Desktop Experience)” install and put it in the C:\Windows\SysWow64 folder of your robot.  Add the following registry:


```plaintext

Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\WOW6432Node\CLSID\{AE054212-3535-4430-83ED-D501AA6680E6}]
@="Shell Name Space ListView"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\WOW6432Node\CLSID\{AE054212-3535-4430-83ED-D501AA6680E6}\InProcServer32]
@=hex(2):25,00,53,00,79,00,73,00,74,00,65,00,6d,00,52,00,6f,00,6f,00,74,00,25,\
  00,5c,00,73,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,65,00,78,00,\
  70,00,6c,00,6f,00,72,00,65,00,72,00,66,00,72,00,61,00,6d,00,65,00,2e,00,64,\
  00,6c,00,6c,00,00,00
"ThreadingModel"="Apartment"
```

### Fix 4
ControlUp Logon Simulator detects admin rights

#### Solution 4
Run the logon simulator as a standard user.

---

### Configuration
Once you’ve implemented all the fixes, install Citrix Workspace App and ControlUp Logon Simulator with an account that is an administrator.

Configure ControlUpLogonSim.  With the simulator open, enter your Storefront details, ensuring to use the “Store” account as seen in the Storefront console.

![](https://theorypc.ca/wp-content/uploads/2019/01/CitrixStorefrontStore.png)
![](https://theorypc.ca/wp-content/uploads/2019/01/LogonSimStoreFront.png)

For the “Resource to Launch” ensure the name matches the display name in Storefront:
![](https://theorypc.ca/wp-content/uploads/2019/01/ResourceSelection.png)


 

In order to avoid **session stealing** in the simulator, each application will require a unique user name.  Setup a unique account per application you are going to test.

![](https://theorypc.ca/wp-content/uploads/2019/02/LogonAccount.png)

 

From here, enter your logon credentials for the account associated with the application.  Run your first test by clicking the green triangle and ensure it works correctly.

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/02/SuccessfulLogonSim.mp4"></video></figure> 

Now that we have a successful run we set “Repeat Test” to ON and save the configuration.

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/02/SaveConfig.mp4"></video></figure> 


I then created another application to monitor by renaming the “Resource to Launch” as another application and saved a second configuration.  I saved all my files to a C:\Swinst folder.

![](https://theorypc.ca/wp-content/uploads/2019/02/2ndApp.png)

The point of all of this is to ensure the simulator is running in an automated fashion.  To do so, we need to be able to configure the simulator to “launch” multiple different applications when the operating system starts.  We have already configured autologon, we’ve setup our configuration files for each application we want to monitor, now we need to set the monitor to auto-start.

Add the following registry key:


```plaintext

Windows Registry Editor Version 5.00
 
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run]
"ApplicationMonitor"="C:\\swinst\\StartAppMonitors.cmd"
```

And create a file “C:\Swinst\StartAppMonitors.cmd” with the following contents:

```shell
START "" "C:\Program Files (x86)\ControlUp Logon Simulator\ControlUpLogonSim.exe" /s /noeula /config=C:\Swinst\Meditech.xml
START "" "C:\Program Files (x86)\ControlUp Logon Simulator\ControlUpLogonSim.exe" /s /noeula /config=C:\Swinst\Hyperspace_2014.xml
```

And watch the magic fly!

<figure class="wp-block-video"><video controls src="/wp-content/uploads/2019/02/Completed.mp4"></video></figure> 


And so the final question and the point of all this work, how much does this consume for our resources?

![](https://theorypc.ca/wp-content/uploads/2019/02/ResourceConsumption.png)

1.1GB of RAM for the ENTIRE system, a peak CPU consumption of 7%, and the processes required to do the monitor use no CPU and only ~55MB of RAM.  Each Citrix process consumes ~20MB of memory and is the most significant consumer of CPU but at the single digit % range.

I anticipate doing some more stress testing to determine what the maximum amount of monitors I can get on one system, but I’m thrilled with these results.  With this one box I would expect to be able to monitor dozens of application…  Maybe a hundred?

In the end, this was a fair bit of work to get this setup on Server Core, but I do believe the savings in resource consumption and overhead reduction will pay off.
