---
id: 2151
title: 'Citrix Storefront &#8211; pass application parameters via URI'
date: 2017-04-22T11:09:47-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/04/22/2150-revision-v1/
permalink: /2017/04/22/2150-revision-v1/
---
Our organization has been exploring upgrading our Citrix environment from 6.5 to 7.X. Â The biggest road blocks we&#8217;ve been experiencing? Â VariousÂ _nuanced_ features in Citrix XenApp 6.5 don&#8217;t work or are not supported in 7.X.

This brings me to this post and an example of the difficulties we are facing, my exploration of solutions to this problem, and our potential solution.

In Citrix Web Interface 5.1+ you can create a URI with a set of application launch parameters and those parameters would be passed to the application. Â This is [detailed in this Citrix article here (CTX123998)](https://support.citrix.com/article/CTX123998).

By following that guide and modifying your Web Interface you can change one of your sites so that it accepts launch parameters. Â You simply enter a specific URI in your browser, and the application would launch with said parameters. Â An example:

<span style="color: #3366ff;">http://bottheory.local/Citrix/XenApp/site/launcher.aspx?CTX_Application=Citrix.MPS.App.XenApp.Notepad&LaunchId=1263894298505&NFuse_AppCommandLine=C:\Windows\WindowsUpdate.log</span>

This allows you to send links around to other people and they can click to automatically launch an application with the parameters specified. Â If you are really unlucky, your org mightÂ document this as an official way to launch a hostedÂ application and this actually gets coded into certain applications. Â So now you may have tens of _localÂ_ apps utilizing this as an acceptable method to launch aÂ _hosted_ application. Â For an organization, thisÂ _may be_Â an acceptable way to launch certain hosted applications since around ~2008 so this &#8220;feature&#8221;, unfortunately, has built up quiteÂ a bit of inertia.

We can&#8217;t let this feature break when we move to storefront. Â We track the number of launches from these hosted applications and it&#8217;s in the hundreds/thousands per day. Â This is a critical and well used feature.

So how does this work in Web Interface and what actually happens?

The modifications that you apply add a new &#8216;query&#8217; to the URI that is picked up. Â This &#8216;query&#8217; is &#8220;NFuse_AppCommandLine&#8221; and theÂ value it isÂ equal to (&#8220;C:\Windows\WindowsUpdate.log&#8221; in my example) is passed into the ICA file.

The ICA file, when launched, will pass the parameter to a special string &#8220;%**&#8221; the is set on the command line of the published application.

[Encoding]  
InputEncoding=UTF8

[WFClient]  
CPMAllowed=On  
ProxyFavorIEConnectionSetting=Yes  
ProxyTimeout=30000  
ProxyType=Auto  
ProxyUseFQDN=Off  
RemoveICAFile=yes  
TransparentKeyPassthrough=Local  
TransportReconnectEnabled=On  
VSLAllowed=On  
Version=2  
VirtualCOMPortEmulation=Off

[ApplicationServers]  
Notepad=

[Notepad]  
Address=10.132.168.100:1494  
AutologonAllowed=ON  
BrowserProtocol=HTTPonTCP  
CGPAddress=*:2598  
ClientAudio=On  
DesiredColor=8  
DesiredHRES=1024  
DesiredVRES=768  
DoNotUseDefaultCSL=On  
FontSmoothingType=0  
InitialProgram=#Notepad  
LPWD=0  
LaunchReference=psXa1C+RcxYDoAawb1LCejpnrWUNJTCmeEVE94K9XRs=  
Launcher=WI  
LocHttpBrowserAddress=!  
LongCommandLine=  
NRWD=16  
ProxyTimeout=30000  
ProxyType=Auto  
SFRAllowed=Off  
SSLEnable=Off  
SessionsharingKey=-rHfm59Fme4OHoZcMhJPz6S  
StartIFDCD=1492880900548  
StartSCD=1492880900548  
TWIMode=On  
Title=Notepad  
TransportDriver=TCP/IP  
UILocale=en  
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

<pre class="lang:default decode:true ">[Encoding]
InputEncoding=UTF8

[WFClient]
CPMAllowed=On
ProxyFavorIEConnectionSetting=Yes
ProxyTimeout=30000
ProxyType=Auto
ProxyUseFQDN=Off
RemoveICAFile=yes
TransparentKeyPassthrough=Local
TransportReconnectEnabled=On
VSLAllowed=On
Version=2
VirtualCOMPortEmulation=Off

[ApplicationServers]
Notepad=

[Notepad]
Address=10.132.168.100:1494
AutologonAllowed=ON
BrowserProtocol=HTTPonTCP
CGPAddress=*:2598
ClientAudio=On
DesiredColor=8
DesiredHRES=1024
DesiredVRES=768
DoNotUseDefaultCSL=On
FontSmoothingType=0
InitialProgram=#Notepad
LPWD=0
LaunchReference=psXa1C+RcxYDoAawb1LCejpnrWUNJTCmeEVE94K9XRs=
Launcher=WI
LocHttpBrowserAddress=!
LongCommandLine=
NRWD=16
ProxyTimeout=30000
ProxyType=Auto
SFRAllowed=Off
SSLEnable=Off
SessionsharingKey=-rHfm59Fme4OHoZcMhJPz6S
StartIFDCD=1492880900548
StartSCD=1492880900548
TWIMode=On
Title=Notepad
TransportDriver=TCP/IP
UILocale=en
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
DriverNameWin32=pdc56n.dll</pre>

&nbsp;

&nbsp;

&nbsp;

But what about the Storefront feature &#8220;[add shortcutsÂ to websites](http://docs.citrix.com/en-us/storefront/3/manage-citrix-receiver-for-web-site/sf-configure-site.html?_ga=1.241941066.975940291.1416084467)&#8220;?

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->