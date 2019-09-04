---
id: 546
title: 'Troubleshooting Citrix Desktop Service - "The Citrix Desktop Service is starting."  and then nothing.'
date: 2015-09-21T16:06:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/21/troubleshooting-citrix-desktop-service-the-citrix-desktop-service-is-starting-and-then-nothing/
permalink: /2015/09/21/troubleshooting-citrix-desktop-service-the-citrix-desktop-service-is-starting-and-then-nothing/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/troubleshooting-citrix-desktop-service.html
blogger_internal:
  - /feeds/7977773/posts/default/491654983081546946
categories:
  - Blog
tags:
  - 2012R2
  - BrokerAgent
  - Citrix
  - Citrix Desktop Service
  - XenDesktop
---
I'm troubleshooting an issue with the Citrix Desktop Service on my home lab. Â I have a Citrix &/XenDesktop 7.6 installation and I have setup a Server 2012 R2 box with the "/servervdi". Â Upon reboot, I see the Registration State as Unregistered.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-GafPMHiCaiA/VgBsjdy9PQI/AAAAAAAABH0/12XC6hpWQJ4/s1600/1.PNG"><img src="http://2.bp.blogspot.com/-GafPMHiCaiA/VgBsjdy9PQI/AAAAAAAABH0/12XC6hpWQJ4/s320/1.PNG" width="320" height="39" border="0" /></a>
</div>

Restarting the Citrix Desktop Service and checking the 'Application' Log for 'Citrix Desktop Service' yields only event ID 1028 - "The Citrix Desktop Service is starting."

However, when I 'Stop' the Citrix Desktop Service the Application event log gives up a few more details:

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Event ID 1003 - Citrix Desktop Service</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">The Citrix Desktop Service failed to initialize communication services required for interaction between this machine and delivery controllers.Â </span>

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">If the problem persists please perform a 'repair' install action or reinstall the Citrix Virtual Desktop Agent. Refer to Citrix Knowledge Base article <a href="http://support.citrix.com/article/CTX119736">CTX119736 Â </a>for Â further information.Â </span>

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Error details:Â </span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Failed to start WCF services. Exception 'Object reference not set to an instance of an object.' of type 'System.NullReferenceException'</span>

Unfortunately, this CTX article gives little to no details on event 1003 and is more of a shotgun attempt at solving issues as opposed to a nice, precise, surgical solution.

From the EventID 1003 we can see the Citrix Desktop Service is trying to reference WCF services and it has failed.

Citrix offers a tool called XDPing to try and diagnose issues, I ran it and had it return the following:

<pre class="lang:default decode:true ">WCF Endpoints: BrokerAgent::
C:\Program Files\Citrix\Virtual Desktop Agent\BrokerAgent.exe
Version Number :7.6.0.5026
 
XenDesktop version 7.6.0.5026
 wsHttpBinding:
 Citrix.Cds.Protocol.Worker.ILaunch:
 http://localhost/Citrix/VirtualDesktopAgent/ILaunch:
    Ping Service: /Citrix/VirtualDesktopAgent/ILaunch
      Connect = Tcp to ::1:80 via ::1 ("Loopback Pseudo-Interface 1") [OK]
      Service = Invalid response [ERROR]
 wsHttpBinding:
 Citrix.Cds.Protocol.Worker.IDynamicDataQuery:
 http://localhost/Citrix/VirtualDesktopAgent/IDynamicDataQuery:
    Ping Service: /Citrix/VirtualDesktopAgent/IDynamicDataQuery
      Connect = Tcp to ::1:80 via ::1 ("Loopback Pseudo-Interface 1") [OK]
      Service = Invalid response [ERROR]
 wsHttpBinding:
 Citrix.Cds.Protocol.Worker.IQueryAgent:
 http://localhost/Citrix/VirtualDesktopAgent/IQueryAgent:
    Ping Service: /Citrix/VirtualDesktopAgent/IQueryAgent
      Connect = Tcp to ::1:80 via ::1 ("Loopback Pseudo-Interface 1") [OK]
      Service = Invalid response [ERROR]
 wsHttpBinding:
 Citrix.Cds.Protocol.Worker.IConfiguration:
 http://localhost/Citrix/VirtualDesktopAgent/IConfiguration:
    Ping Service: /Citrix/VirtualDesktopAgent/IConfiguration
      Connect = Tcp to ::1:80 via ::1 ("Loopback Pseudo-Interface 1") [OK]
      Service = Invalid response [ERROR]
 wsHttpBinding:
 Citrix.Cds.Protocol.Worker.ISessionManager:
 http://localhost/Citrix/VirtualDesktopAgent/ISessionManager:
    Ping Service: /Citrix/VirtualDesktopAgent/ISessionManager
      Connect = Tcp to ::1:80 via ::1 ("Loopback Pseudo-Interface 1") [OK]
      Service = Invalid response [ERROR]
 netNamedPipeBinding:
 Citrix.Cds.Protocol.Skylight.ISkylightLaunch:
 net.pipe://localhost/CitrixSkylight:
Endpoint -> not Tested - net.pipe://localhost/CitrixSkylight
 [OK]
 
--------------------------------------------------------------------
Workstation Services::
 
  Service  : BrokerAgent ("Citrix Desktop Service")
    Status = Win32OwnProcess, Running [OK]
    Prereq =
      LanmanWorkstation (Win32ShareProcess), Running
 
  Service  : Citrix Encryption Service ("Citrix Encryption Service")
    Status = Win32OwnProcess, Running [OK]
    Prereq =
      Winmgmt (Win32ShareProcess), Running
 
  Service  : cpsvc ("Citrix Print Manager Service")
    Status = Win32OwnProcess, Running [OK]
    Prereq =
      Spooler (Win32OwnProcess, InteractiveProcess), Running
      PorticaService (Win32OwnProcess), Running
      RpcSs (Win32ShareProcess), Running
 
--------------------------------------------------------------------
DNS Lookups for Local Machine::
 
  Host Name  : Q87M-E.bottheory.local
  Address #0 = fe80::9400:9ce6:4ca:36f4%17 (rDNS: Q87M-E.bottheory.local) [OK]
  Address #1 = 192.168.1.99 (rDNS: Q87M-E.bottheory.local) [OK]
 
--------------------------------------------------------------------
Client Details::
   (Session ID) (Status)    (Name)   (Client IP Address):
 
    No user session detected
--------------------------------------------------------------------
Event Log Check::
   A number of importent errors/warning(2) have been logged into the event log in the last hour, please check the logs for more details
 
--------------------------------------------------------------------
Windows Firewall Settings::
 
Status : Disabled
 
Current Profile name : Domain
--------------------------------------------------------------------
XenDesktop Farm::
 
  Farm GUID (GPO)   : Not Set
  Farm GUID (local) : NOT SET
  Farm GUID In Use  : NOT SET
--------------------------------------------------------------------
Registry Based Configurations::
 
Registry based Controller list (ListOfDDCs) : [Not Conigured]
 [Not Conigured]
  It is not possible to enurmerate DDC list from VDA [ERROR]
--------------------------------------------------------------------
Controllers (manually specified)::
 
  Controller: ctxzdc01.bottheory.local:0
    DNS Lookup(ctxzdc01.bottheory.local):
      Host Name  = ctxzdc01.bottheory.local
      Address #0 = 192.168.1.30 (rDNS: ctxzdc01.bottheory.local) [OK]
    Ping Service: /Citrix/CdsController/IRegistrar
      Connect = Unable to open connection to ctxzdc01.bottheory.local:0
 [ERROR]
 
 
--------------------------------------------------------------------
Summary::
 
    Checking version : You are using the latest version. [OK]
    Service = Invalid response [ERROR]
    Service = Invalid response [ERROR]
    Service = Invalid response [ERROR]
    Service = Invalid response [ERROR]
    Service = Invalid response [ERROR]
    A number of importent errors/warning(2) have been logged into the event log in the last hour, please check the logs for more details [WARNING]
    It is not possible to enurmerate DDC list from VDA [ERROR]
    Connect = Unable to open connection to ctxzdc01.bottheory.local:0 [ERROR]
 
Number of messages reported = 9</pre>

Googling the WCF errors with HTTP/1.1 and Error 503 results in lots of information on reconfiguring your IIS. Â I'm not convinced this is the issue so I soldiered on...

When I procmon on 'BrokerAgent.exe' I see a few curious entires that maybe associated with '<span style="font-family: 'Courier New', Courier, monospace; font-size: x-small;">System.NullReferenceException</span>' (aka, not found) and those are some permissions stating that access is denied to some registry keys and/or some 'Name Not Found' on some CLSID items.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-k7EQjMdNqY4/VgBwWVZTAmI/AAAAAAAABIA/-IlW95sJFjc/s1600/2.PNG"><img src="http://3.bp.blogspot.com/-k7EQjMdNqY4/VgBwWVZTAmI/AAAAAAAABIA/-IlW95sJFjc/s400/2.PNG" width="400" height="212" border="0" /></a>
</div>

During this capture I can see a 'BrokerAgent.config' file referenced. Â ("C:\Program Files\Citrix\Virtual Desktop Agent\BrokerAgent.exe.config" ) Diving into it reveals some additional logging we can enable:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="http://2.bp.blogspot.com/-r-mncaP7b1o/VgBw1mBDirI/AAAAAAAABII/2d4lbZq6Tp0/s1600/3.PNG"><img src="http://2.bp.blogspot.com/-r-mncaP7b1o/VgBw1mBDirI/AAAAAAAABII/2d4lbZq6Tp0/s320/3.PNG" width="320" height="225" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      Verbose logging disabled by default
    </td>
  </tr>
</table>

If we 'enable' the debug portions and create the C:cdsLogs folder we can get some more information on what is going wrong.  
<a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-17zy6-ytTlI/VgBxA1tWgnI/AAAAAAAABIQ/DtICtowp5aI/s1600/4.PNG"><img src="http://4.bp.blogspot.com/-17zy6-ytTlI/VgBxA1tWgnI/AAAAAAAABIQ/DtICtowp5aI/s320/4.PNG" width="320" height="94" border="0" /></a>  
Logging Enabled

Stopping the 'Citrix Desktop Service', editing the .config file, creating the C:\cdslogs folder and starting the service yielded additional information.

<pre class="lang:default decode:true ">21/09/15 15:04:35.913 3796 2676: BrokerAgent:AgentService() - entry.
21/09/15 15:04:35.913 3796 2676: BrokerAgent:AgentService.InitializeComponent entry
21/09/15 15:04:35.913 3796 2676: BrokerAgent:AgentService.InitializeComponent exit
21/09/15 15:04:35.913 3796 2676: BrokerAgent:AgentService() - exit
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.OnStart entered VdaExecMode = Normal, portNumber = -1, copmuterName = , computerSid = 
21/09/15 15:04:35.960 3796 8496: BrokerAgent:RegisterEventSource: eventLogSource = Citrix Desktop Service
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.ActualOnStart After register event source. Service running in Normal mode
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.ActualOnStart Before opening LOCAL registry keys
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.ActualOnStart Before opening POLICY registry keys
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.ActualOnStart After policy registry key open
21/09/15 15:04:35.960 3796 8496: BrokerAgent:Delaying for 0 ms before starting Broker Agent service
21/09/15 15:04:35.960 3796 8496: BrokerAgent:AgentService.ActualOnStart Before open Information Manager
21/09/15 15:04:35.960 3796 8496: BrokerAgent:Event log control registry key is not present, so defaults will be used
21/09/15 15:04:36.069 3796 8496: BrokerAgent:PersistentDataLocation = C:/ProgramData/Citrix/PvsAgent/LocallyPersistedData\BrokerAgentInfo
21/09/15 15:04:36.101 3796 8496: BrokerAgent:AgentService.ActualOnStart Before Setting up Event logging
21/09/15 15:04:36.101 3796 8496: BrokerAgent:RegisterEventSource: eventLogSource = Citrix Desktop Service
21/09/15 15:04:36.116 3796 8904: BrokerAgent:TimedEventLogWorkItemManager::ProcessWorkItemThreadBody - Entered
21/09/15 15:04:36.116 3796 8904: BrokerAgent:TimedEventLogWorkItemManager::ProcessWorkItemThreadBody - Processing
21/09/15 15:04:36.116 3796 8904: BrokerAgent:TimedEventLogWorkItemManager::ProcessWorkItemThreadBody - Sleeping 86399984ms
21/09/15 15:04:36.116 3796 8904: BrokerAgent:TimedEventLogWorkItemManager::ProcessWorkItemThreadBody - Processing
21/09/15 15:04:36.116 3796 8904: BrokerAgent:TimedEventLogWorkItemManager::ProcessWorkItemThreadBody - Sleeping 86399984ms
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress brokered session messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress brokered session messages Configuration to default False
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress Service Startup Messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress Service Startup Messages Configuration to default True
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress Obtained Controller List Messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress Obtained Controller List Messages Configuration to default True
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress stack connection status messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress stack connection status messages Configuration to default True
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress Failed to registry with any controller messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress Failed to registry with any controller messages Configuration to default True
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Time based per controler authorization messages suppression configuration to defulat SuppressThresholdCount:'3', ThresholdWindowDuration:'00:02:00', UnsuppressQuietDuration:'00:10:00'
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Suppress per controller authorization messages Configuration not yet determined; reading from registry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Setting Suppress per controller authorization messages Configuration to default True
21/09/15 15:04:36.116 3796 8496: BrokerAgent:AgentService.ActualOnStart Check that we are running as Network Service
21/09/15 15:04:36.116 3796 8496: BrokerAgent:AgentService.ActualOnStart Before Create controller channel factory
21/09/15 15:04:36.116 3796 8496: BrokerAgent:ViaBoxMode = False
21/09/15 15:04:36.116 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of Stack Manager
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Singleton get - entry
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Create
21/09/15 15:04:36.116 3796 8496: BrokerAgent:StackManager constructor: Enter/Exit
21/09/15 15:04:36.116 3796 8496: BrokerAgent:Singleton get - exit
21/09/15 15:04:36.116 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of Registration Manager
21/09/15 15:04:36.132 3796 8496: BrokerAgent:AgentHeartBeat.AgentHeartBeat: Enter
21/09/15 15:04:36.132 3796 8496: BrokerAgent:AgentHeartBeat.AgentHeartBeat: Exit
21/09/15 15:04:36.132 3796 8496: BrokerAgent:VdaState.ClearVdaStateInf - enter
21/09/15 15:04:36.132 3796 8496: BrokerAgent:Clear VDA State Values
21/09/15 15:04:36.132 3796 8496: BrokerAgent:PublishWmiVdaRegistrationInfo called
21/09/15 15:04:36.257 3796 8496: BrokerAgent:VdaState.ChangeRegisterVdaStateInfo - enter: registeredMode NotRegistered
21/09/15 15:04:36.257 3796 8496: BrokerAgent:PublishWmiVdaRegistrationInfo called
21/09/15 15:04:36.257 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of Notification Manager
21/09/15 15:04:36.257 3796 8496: BrokerAgent:AgentService.ActualOnStart Before initialization of Command Queue
21/09/15 15:04:36.257 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of Plugin Manager
21/09/15 15:04:36.304 3796 8496: BrokerAgent:PluginManager.Constructor: Enter
21/09/15 15:04:36.304 3796 8496: BrokerAgent:PluginManager.Constructor: Subscribing to RegistrationNotification
21/09/15 15:04:36.304 3796 8496: BrokerAgent:PluginManager.Constructor: Exit
21/09/15 15:04:36.304 3796 8496: BrokerAgent:Initialize StackManager: Enter(m_StackManagerInitialized: False)
21/09/15 15:04:36.304 3796 8496: BrokerAgent:AgentToStack constructor: Enter/Exit
21/09/15 15:04:36.304 3796 8496: BrokerAgent:ComClassFactory - entry
21/09/15 15:04:36.304 3796 8496: BrokerAgent:ComClassFactory - exit
21/09/15 15:04:36.304 3796 8496: BrokerAgent:RegisterTypeForComClients - entry
21/09/15 15:04:36.304 3796 8496: BrokerAgent:RegisterTypeForComClients - exit
21/09/15 15:04:36.304 3796 8496: BrokerAgent:ViaBoxMode = False
21/09/15 15:04:36.304 3796 8496: BrokerAgent:Initialize StackManager: Exit(AgentProcessId:3796)
21/09/15 15:04:36.304 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of Monitor Manager
21/09/15 15:04:36.304 3796 8496: BrokerAgent:ViaBoxMode = False
21/09/15 15:04:36.319 3796 8496: BrokerAgent:AgentService.ActualOnStart Before start of background start up thread
21/09/15 15:04:36.319 3796 8496: BrokerAgent:AgentService.ActualOnStart exit
21/09/15 15:04:36.319 3796 7336: BrokerAgent:DoAgentStartWcfServices: Setting up WCF services
21/09/15 15:04:36.335 3796 7336: BrokerAgent:Setting up ALL LaunchManager WCF service
21/09/15 15:04:36.335 3796 7336: BrokerAgent:AgentToStack.ConnectToStackControlCOMServer: Enter
21/09/15 15:04:36.351 3796 7336: BrokerAgent:StackManager.ConnectToStackControlComServerAndVerify: COM exception System.InvalidCastException: Unable to cast COM object of type 'System.__ComObject' to interface type 'Citrix.StackControlService.StackControl'. This operation failed because the QueryInterface call on the COM component for the interface with IID '{BEE5F9CD-A777-47C7-BA5A-CDD82FFEC4D8}' failed due to the following error: Error loading type library/DLL. (Exception from HRESULT: 0x80029C4A (TYPE_E_CANTLOADLIBRARY)).
   at Citrix.Cds.BrokerAgent.AgentToStack.ConnectToStackControlCOMServer()
   at Citrix.Cds.BrokerAgent.StackManager.ConnectToStackControlComServer(StackCapabilities& actualStackCapabilities, Int32 retryCount)</pre>

We have a 'COM exception'. Â The nice thing about this log file is we can compare the time stamps to the procmon logs and determine what was happening when this failed.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-m_4BvUX74Vg/VgBzOviI0_I/AAAAAAAABIc/bHwCefWi7mE/s1600/5.PNG"><img src="http://1.bp.blogspot.com/-m_4BvUX74Vg/VgBzOviI0_I/AAAAAAAABIc/bHwCefWi7mE/s400/5.PNG" width="400" height="182" border="0" /></a>
</div>

It appears we are missing [SCService64.exe](http://www.basvankaam.com/2014/12/15/xendesktop-7-x-fma-internals-continued-the-server-vda-in-more-detail/) from our Citrix installation. Â What is 'SCService64.exe'? Â The registry tells me it's the 'IStackControl Type Library'. Â Which matches up with the error 'ConnectToStackControlCOMServer'.

So, it appears we need to install SCService64.exe. Â I do not know why or how it went missing but I suspect we can copy it over or extract from the Citrix source files if needed.

To extract the SCService64.exe source file, we can create an administrative installation of the "TS" VDA:

msiexec /a "\x79-serversoftwareCitrix&\_and\_XenDesktop7\_6x64Virtual Desktop ComponentsTSIcaTS\_x64.msi"  
This makes a folder on the root of the C: called "Citrix" which installs all the files there:

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-TlXtkBNxOX0/VgB9UNlFwuI/AAAAAAAABIs/EIhhunNCepk/s1600/6.PNG"><img src="http://3.bp.blogspot.com/-TlXtkBNxOX0/VgB9UNlFwuI/AAAAAAAABIs/EIhhunNCepk/s320/6.PNG" width="320" height="220" border="0" /></a>
</div>

<div>
</div>

I installed the Citrix VDA "WS" with /servervdi for some testing, but prior to that I had the regular "TS" VDA installed. Â Perhaps some combination of my installation/uninstallation caused my issues.

Anyways, copying the SCService64.exe to the C:\Program Files (x86)\Citrix\System32 folder and restarting the 'Citrix Desktop Service' resulted in...

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-4mV9RKsi_VU/VgB9n5FLy4I/AAAAAAAABI0/Ddqq3qX5Ecw/s1600/7.PNG"><img src="http://3.bp.blogspot.com/-4mV9RKsi_VU/VgB9n5FLy4I/AAAAAAAABI0/Ddqq3qX5Ecw/s320/7.PNG" width="320" height="67" border="0" /></a>
</div>

Registered, Hurray!

And what about our log file?

Previously it died around 'Setting up ALL LaunchManager WCF service'; this time we see:

<pre class="lang:default decode:true ">21/09/15 15:48:44.282 3728 6880: BrokerAgent:DoAgentStartWcfServices: Setting up WCF services
21/09/15 15:48:44.282 3728 6880: BrokerAgent:Setting up ALL LaunchManager WCF service
21/09/15 15:48:44.298 3728 6880: BrokerAgentLaunchStore:LaunchStore:InitializeLaunchStore: Entry, Initializing CbpVersion = CBPv1_5, SessionMode = SingleSession
21/09/15 15:48:44.298 3728 6880: BrokerAgentLaunchStore:LaunchStore:InitializeLaunchStore: Exit
21/09/15 15:48:44.313 3728 6880: BrokerAgent:Adding authorization manager to service: LaunchManager
21/09/15 15:48:44.345 3728 6880: BrokerAgent:Setting up ALL QueryManager WCF service
21/09/15 15:48:44.345 3728 6880: BrokerAgent:Adding authorization manager to service: QueryManager
21/09/15 15:48:44.345 3728 6880: BrokerAgent:Setting up ALL ConfigurationManager WCF service
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Adding authorization manager to service: ConfigurationManager
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Setting up ALL SessionManager WCF service
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Adding authorization manager to service: SessionManager
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Setting up ALL QueryAgentManager WCF service
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Adding authorization manager to service: QueryAgentManager
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Setting up ALL SkylightManager WCF service
21/09/15 15:48:44.360 3728 6880: BrokerAgent:Adding authorization manager to service: SkylightManager
21/09/15 15:48:44.360 3728 6880: BrokerAgent:DoAgentStartWcfServices: WCF services started
21/09/15 15:48:44.376 3728 6880: BrokerAgent:EventLogManager decided to log event WorkerAgentGoodWcf of type Information with arguments:.This is based on event log groups Wcf
21/09/15 15:48:44.376 3728 6880: BrokerAgent:DoAgentStartWcfServices: Stack Controller WCF services started
21/09/15 15:48:44.376 3728 6880: BrokerAgent:AgentService.DoAgentStartVdaParentOuDetection - entry; operation mode Brokered</pre>

Hurray! Â It continues and operates without issue.

Be sure to re-disabled the logging on the BrokerAgent.config file and restart the service because I've found this BA_1.log can get to become a huge file.

Running XDPing this time results in [OK] across where the errors once were.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->