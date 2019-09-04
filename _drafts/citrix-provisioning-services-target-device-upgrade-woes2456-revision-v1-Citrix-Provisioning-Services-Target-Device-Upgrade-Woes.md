---
id: 2487
title: Citrix Provisioning Services Target Device Upgrade Woes
date: 2017-06-27T09:21:11-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/06/27/2456-revision-v1/
permalink: /2017/06/27/2456-revision-v1/
---
We are running Citrix PVS 7.7 and we are attempting to upgrade to Citrix PVS 7.13 for the vDisks. Â Starting with PVS 7.7 you are supposed to be able to do an in-place upgrade of the tools. Â Our experience with this has been less than positive, with a success rate of ~50%. Â This post is the issues we encountered and how we solved it for the other 50%.

* * *

Issue #1:

Error 2203. Database: C:\Windows\Installer\111cbf.ipi. Â Cannot open database file. Â System error -2147287037.

<div style="width: 1016px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2487-95" width="1016" height="712" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/06/error.mp4?_=95" /><a href="http://theorypc.ca/wp-content/uploads/2017/06/error.mp4">http://theorypc.ca/wp-content/uploads/2017/06/error.mp4</a></video>
</div>

When I look for that ipi file I find it is not present. Â Click &#8216;OK&#8217; results in nothing happening. Â Attempting to install PVS 7.13 as an in place upgrade results in Error 1923.Service Citrix PVS Device Service (BNDevice) could not be installed. Â Verify that you have sufficient privileges to install system services.

In an attempt to move this along I stopped the BNDevice service and manually specified a deletion (&#8220;sc delete bndevice&#8221;). Â This allowed the install to proceed without issue.

* * *

Issue #2:

Uninstalling PVS 7.13 Target Device Software fails with &#8216;Product key not found&#8217;.

<img class="aligncenter size-full wp-image-2458" src="http://theorypc.ca/wp-content/uploads/2017/06/Error2.png" alt="" width="399" height="149" srcset="http://theorypc.ca/wp-content/uploads/2017/06/Error2.png 399w, http://theorypc.ca/wp-content/uploads/2017/06/Error2-300x112.png 300w" sizes="(max-width: 399px) 100vw, 399px" /> 

Doing a Procmon trace I discovered that PVS 7.13 requires a registry key and value to uninstall cleanly:

<img class="aligncenter size-full wp-image-2459" src="http://theorypc.ca/wp-content/uploads/2017/06/TargetDir.png" alt="" width="798" height="412" srcset="http://theorypc.ca/wp-content/uploads/2017/06/TargetDir.png 798w, http://theorypc.ca/wp-content/uploads/2017/06/TargetDir-300x155.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/TargetDir-768x397.png 768w" sizes="(max-width: 798px) 100vw, 798px" /> 

For whatever reason, this key and value do not get created and do not exist on an upgrade or clean install but are required to uninstall. Â Manually adding the key allows the PVS Target Device software to uninstall cleanly.

* * *

Issue #3:

<img class="aligncenter size-full wp-image-2471" src="http://theorypc.ca/wp-content/uploads/2017/06/pick_another_vdisk.png" alt="" width="622" height="436" srcset="http://theorypc.ca/wp-content/uploads/2017/06/pick_another_vdisk.png 622w, http://theorypc.ca/wp-content/uploads/2017/06/pick_another_vdisk-300x210.png 300w" sizes="(max-width: 622px) 100vw, 622px" /> 

When using the &#8220;Provisioning Services Imaging Wizard&#8221; an error occurs &#8220;The vDisk has no volumes to copy to. Â Pick another vDisk.&#8221;

This error occurs because our uninstall did not remove the Citrix Storage controller driver, and when we did an install it added another Citrix storage controller. Â This new controller adds an additional disk but since the disk is in use from the original connection it comes up as &#8220;offline&#8221;.

<img class="aligncenter size-full wp-image-2472" src="http://theorypc.ca/wp-content/uploads/2017/06/PVS_Disks.png" alt="" width="1104" height="610" srcset="http://theorypc.ca/wp-content/uploads/2017/06/PVS_Disks.png 1104w, http://theorypc.ca/wp-content/uploads/2017/06/PVS_Disks-300x166.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/PVS_Disks-768x424.png 768w" sizes="(max-width: 1104px) 100vw, 1104px" /> 

If you attempt to bring the Disk Online you get an &#8220;Incorrect function.&#8221; error:

<img class="aligncenter size-full wp-image-2474" src="http://theorypc.ca/wp-content/uploads/2017/06/Online.png" alt="" width="162" height="97" />  
<img class="aligncenter size-large wp-image-2475" src="http://theorypc.ca/wp-content/uploads/2017/06/IncorrectFunctions.png" alt="" width="192" height="139" /> 

Device Manager shows multiple devices:

<div id="attachment_2473" style="width: 325px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2473" class="wp-image-2473 size-full" src="http://theorypc.ca/wp-content/uploads/2017/06/devicemanager.png" alt="" width="315" height="407" srcset="http://theorypc.ca/wp-content/uploads/2017/06/devicemanager.png 315w, http://theorypc.ca/wp-content/uploads/2017/06/devicemanager-232x300.png 232w" sizes="(max-width: 315px) 100vw, 315px" /></p> 
  
  <p id="caption-attachment-2473" class="wp-caption-text">
    There should only exist 1 of these Citrix devices.
  </p>
</div>

To resolve this issue, simply select right-click <span style="text-decoration: underline;"><strong>one</strong></span> of the &#8220;Citrix Virual Hard Disk Adapter&#8221; and select &#8220;Uninstall&#8221;.

<img class="aligncenter size-full wp-image-2477" src="http://theorypc.ca/wp-content/uploads/2017/06/uninstall_device.png" alt="" width="320" height="138" srcset="http://theorypc.ca/wp-content/uploads/2017/06/uninstall_device.png 320w, http://theorypc.ca/wp-content/uploads/2017/06/uninstall_device-300x129.png 300w" sizes="(max-width: 320px) 100vw, 320px" /> 

This may cause you toÂ reboot. Â On reboot you should only have a single device listed, and the offline disk should be gone from Disk Management. Â At this point you can run the Imaging Wizard without issue.

* * *

Issue #4

&#8220;Citrix Provisioning Services Target Device x64&#8221; software is not displayed in &#8216;Programs and Features&#8217;, but it is obviously installed because the files exist, the registry keys exist, and the program is running.

<div id="attachment_2479" style="width: 1264px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2479" class="wp-image-2479 size-full" src="http://theorypc.ca/wp-content/uploads/2017/06/Screen-Shot-2017-06-27-at-7.13.23-AM.png" alt="" width="1254" height="709" srcset="http://theorypc.ca/wp-content/uploads/2017/06/Screen-Shot-2017-06-27-at-7.13.23-AM.png 1254w, http://theorypc.ca/wp-content/uploads/2017/06/Screen-Shot-2017-06-27-at-7.13.23-AM-300x170.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/Screen-Shot-2017-06-27-at-7.13.23-AM-768x434.png 768w" sizes="(max-width: 1254px) 100vw, 1254px" /></p> 
  
  <p id="caption-attachment-2479" class="wp-caption-text">
    It should be listed just under &#8220;Citrix Profile Management&#8221;
  </p>
</div>

If you attempt to run the removal or modifyPath registry command

<div style="width: 1140px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-2487-96" width="1140" height="641" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/06/PVSUninstallError.mp4?_=96" /><a href="http://theorypc.ca/wp-content/uploads/2017/06/PVSUninstallError.mp4">http://theorypc.ca/wp-content/uploads/2017/06/PVSUninstallError.mp4</a></video>
</div>

You get an error &#8220;Windows installer has stopped working&#8221;. Â If you try and continue you MAY get stopped at &#8220;Error 1723. There is a problem with this Windows Installer package. Â A DLL required for this install to complete could not be run. Â Contact your support personnel or package vendor. Â Action BNNS\_Uninstall.FA231996\_1469\_4817\_B7F3\_61A648A18C07, entry: fnBNNS\_Uninstall, library: C:\Windows\Installer\MSI6AFF.tmp&#8221;

As far as I can see, when in this state, ANY custom action by the MSI produces anÂ error. Â The Citrix Provisioning Services installer has numerous custom actions so this comes up a few times. Â The first custom action that causes a crashÂ checks to see if a reboot is pending and presents you a dialog if it detects a pending reboot. Â I&#8217;ve not been able to find much about the second action&#8230;

The only way I&#8217;ve found to fix this issue is to &#8216;rip&#8217; Citrix PVS Target Device software out of the system. Â To ensure the bits and registry keys are all captured, I install the same version over top. Â My hope is the &#8216;reinstall&#8217; overtop will be a perfect overlay and any corruption or breaking will be corrected by the new install. Â So far, this seems to be a successful strategy.

Here is how I ripped the software out.

  1. Reverse Image the vDisk
  2. Download [MSIZap](https://msdn.microsoft.com/en-us/library/windows/desktop/aa370523(v=vs.85).aspx) (from the [Microsoft Windows SDK for Windows 7 and .Net Framework 3.5 SP1](https://www.microsoft.com/en-us/download/details.aspx?id=3138))
  3. Download [SetACL](https://helgeklein.com/setacl/) from Helge Klein
  4. Extract both to the same folder and save this script to it (it targets PVS 7.7 specifically) and then run it: <pre class="lang:batch decode:true">pushd %~dp0
:forcibly removes Citrix PVS Target Device software version 7.7

msizap TWA! {3676AF4D-C2A9-4C32-AC56-9BBC4BDA4566}
msizap T {3676AF4D-C2A9-4C32-AC56-9BBC4BDA4566}

rmdir /s /q "C:\windows\Downloaded Installations\{E0715049-3D3B-4EEC-A34E-7AE6EE4DDD93}"

::MSIZap destroys permissions -- take ownership of all keys for EVERYONE then reset them to defaults
SetACL.exe -on "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Components" -ot reg -actn setowner -ownr "n:S-1-1-0" -rec yes
SetACL.exe -on "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Components" -ot reg -actn rstchldrn -rst dacl -rec yes
SetACL.exe -on "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Components" -ot reg -actn setowner -ownr "n:S-1-5-18" -rec yes


:: delete PVS services
sc delete cnicteam
net stop bndevice /y
sc delete bndevice
taskkill /im bndevice.exe /f
taskkill /im statustray.exe /f
net stop bnistack
sc delete bnistack
net stop cfsdep2
sc delete cfsdep2
net stop cvhdmp
sc delete cvhdmp
net stop Citrix.Xip.ClientService
sc delete Citrix.Xip.ClientService

reg delete HKLM\System\CurrentControlSet\services\eventlog\Application\Citrix.Xip.ClientService /f

del /q C:\Windows\System32\drivers\bnistack6.sys
rmdir /s /q "C:\Program Files\Citrix\Provisioning Services"

:you probably need to reboot and delete the provisioning services folder because BNDevice.exe is locked by svchost and cannot be deleted

pause
popd</pre>
    
    &nbsp;</li> </ol> 
    
    So a little explanation on why the script is needed and what it&#8217;s doing. Â The MSIZap removes the keys that &#8216;registers&#8217; the product with the system. Â This allows us to run the MSI as if it&#8217;s installing on a clean system. Â This passes the first check of the MSI which would tell you to &#8220;Repair or Remove&#8221; instead.
    
    However, after running MSIZap permissions are futzed pretty hard in the &#8216;Installer\Components&#8221; keys. Â They just seem to be outright removed. Â My understanding of the [TWA! parameters](https://technet.microsoft.com/en-us/library/cc783013(v=ws.10).aspx)Â are that permissions are supposed to be changed, but not removed. Â However, specifying TWA! seems to just change permissions and not &#8216;delete&#8217; anything.
    
<img class="aligncenter size-full wp-image-2482" src="http://theorypc.ca/wp-content/uploads/2017/06/msizap.png" alt="" width="665" height="330" srcset="http://theorypc.ca/wp-content/uploads/2017/06/msizap.png 665w, http://theorypc.ca/wp-content/uploads/2017/06/msizap-300x149.png 300w" sizes="(max-width: 665px) 100vw, 665px" /> 
    
    So I specify just theÂ &#8220;T&#8221; command with MSIZap afterwards and thisÂ _will_Â actually remove files and registry keys.<img class="aligncenter size-full wp-image-2483" src="http://theorypc.ca/wp-content/uploads/2017/06/msiZapT.png" alt="" width="664" height="303" srcset="http://theorypc.ca/wp-content/uploads/2017/06/msiZapT.png 664w, http://theorypc.ca/wp-content/uploads/2017/06/msiZapT-300x137.png 300w" sizes="(max-width: 664px) 100vw, 664px" /> 
    
    However, it seems to be semi-broken because the registry keys it touches are left broken without Ownership or Rights permissions applied. Â Attempting to install when in this state generates messages like so:
    
<img class="aligncenter size-full wp-image-2484" src="http://theorypc.ca/wp-content/uploads/2017/06/badPerms_Winstaller.png" alt="" width="504" height="377" srcset="http://theorypc.ca/wp-content/uploads/2017/06/badPerms_Winstaller.png 504w, http://theorypc.ca/wp-content/uploads/2017/06/badPerms_Winstaller-300x224.png 300w" sizes="(max-width: 504px) 100vw, 504px" /> 
    
    &#8220;Error 1402. Could not open key: UNKNOWN\Components\84029B5E95851FA4EADA9BE7FB000B78\D4FA67639A2C23C4CA65B9CBB4AD5446. Verify that you have sufficient access to that key or contact your support personnel.&#8221;
    
    If you navigate to that key you&#8217;ll find you get access is denied and exploring the permissions on the key shows other errors like no ownership set:
    
    <div style="width: 1140px;" class="wp-video">
      <video class="wp-video-shortcode" id="video-2487-97" width="1140" height="641" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2017/06/BadPerms.mp4?_=97" /><a href="http://theorypc.ca/wp-content/uploads/2017/06/BadPerms.mp4">http://theorypc.ca/wp-content/uploads/2017/06/BadPerms.mp4</a></video>
    </div>
    
    To correct these permissions being broken I run 3 SetACL commands.
    
    The first command sets ownership on all keys to &#8216;EVERYONE&#8217;. Â This corrects ownership on the bad keys so we don&#8217;t have error when setting permissions.
    
    The second command resets inheritance so that permissions are now inherited from the root key.
    
    The third command sets ownership back to SYSTEM from EVERYONE.
    
    Lastly, it is necessary to remove the services as the PVS installer will check to see if they exist. Â If they do they will stop the install. Â We found we could not delete the BNDevice.exe because it was being held open by svchost.exe. Â Rebooting fixed that and we could delete it and the &#8220;C:\Program Files\Citrix\Provisioning Services&#8221; folder.
    
    And than, with all that, we can now install PVS 7.7 overtop of itself, and then removals and in-place upgrades work as expected.
    
    <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->