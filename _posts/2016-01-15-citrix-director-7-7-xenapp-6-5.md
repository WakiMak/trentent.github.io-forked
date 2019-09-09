---
id: 533
title: Citrix Director 7.7 & XenApp 6.5
date: 2016-01-15T15:59:00-06:00
author: trententtye
layout: post
permalink: /2016/01/15/citrix-director-7-7-xenapp-6-5/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2016/01/citrix-director-77-&-65.html
blogger_internal:
  - /feeds/7977773/posts/default/3852527102026683867
image: /wp-content/uploads/2016/01/Picture1-1.png
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Citrix Director

---
Citrix has 'replaced' Edgesight with a new tool called Citrix <span style="text-decoration: line-through;">Desktop</span>Director. It's a monitoring tool that provides help desk or other type of technicians with data on what's going on in user sessions (when referring to &). Although vastly inferior to [ControlUp](http://controlup.com/), it is free.

But it's not without poor documentation and its own tales of woe. We have Citrix DesktopDirector 2.1 currently live in our environment. Our environment is purely & 6.5 (at the moment).

For version 2.1, logins are incredibly slow. Enumerating any sort of information on DesktopDirector is horribly slow. It's just incredi-bad.

Enter Citrix Director <span style="text-decoration: line-through;">7.6 </span>7.7; lots of promises to overcome the failings of the previous version.

First, installing Citrix Director 7.7 for & 6.5:

1) Launch the AutoRun (AutoSelect.exe) installation file

2)[<img src="/wp-content/uploads/2016/01/Picture1-1-300x219.png" border="0" />](/wp-content/uploads/2016/01/Picture1-1.png)  
Select '**Start**' for &

3)[<img src="/wp-content/uploads/2016/01/Picture2-1-300x221.png" border="0" />](/wp-content/uploads/2016/01/Picture2-1.png)  
Select '**Citrix Director**'

4)[<img src="/wp-content/uploads/2016/01/Picture3-1-300x226.png" border="0" />](/wp-content/uploads/2016/01/Picture3-1.png)  
Agree to the licensing agreement and click '**Next**'

5)[<img src="/wp-content/uploads/2016/01/Picture4-1-300x226.png" border="0" />](/wp-content/uploads/2016/01/Picture4-1.png)  
Select '**Next**'

6)[<img src="/wp-content/uploads/2016/01/Picture5-1-300x226.png" border="0" />](/wp-content/uploads/2016/01/Picture5-1.png)  
Without entering any information, select '**Next**'

7)[<img src="/wp-content/uploads/2016/01/Picture6-1-300x226.png" border="0" />](/wp-content/uploads/2016/01/Picture6-1.png)  
Select '**Next**'

8)[<img src="/wp-content/uploads/2016/01/Picture7-1-300x226.png" border="0" />](/wp-content/uploads/2016/01/Picture7-1.png)  
Select '**Next**' for the firewall rules

9)[<img src="/wp-content/uploads/2016/01/Picture8-1-300x225.png" border="0" />](/wp-content/uploads/2016/01/Picture8-1.png)  
Select '**Install**'

10) [<img src="/wp-content/uploads/2016/01/Picture9-1-300x227.png" border="0" />](/wp-content/uploads/2016/01/Picture9-1.png)  
Wait for the install to complete

11) [<img src="/wp-content/uploads/2016/01/Picture10-1-300x287.png" border="0" />](/wp-content/uploads/2016/01/Picture10-1.png)  
Open **IIS Manager** on the Director Server. Select the **Director** site under **Default Web Site** and double-click on '**Application Settings**'

12)[<img src="/wp-content/uploads/2016/01/Picture11-1-300x131.png" border="0" />](/wp-content/uploads/2016/01/Picture11-1.png)  
Under '**Actions**' click '**Add**'

13)[<img src="/wp-content/uploads/2016/01/Picture12-1-300x171.png" border="0" />](/wp-content/uploads/2016/01/Picture12-1.png)  
For '**Name**' enter '**Service.AutoDiscoveryAddressesXA**' and for value put the IP of the local ZDC server

14)[<img src="/wp-content/uploads/2016/01/Picture13-1-300x196.png" border="0" />](/wp-content/uploads/2016/01/Picture13-1.png)  
If you are not setting up certificates on the server for SSL, you can disable the SSL verification by changing the UI.EnableSslCheck to false.

15) Reset IIS from the command line.

16) [<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B9.41.03-2BPM-1-300x186.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B9.41.03-2BPM-1.png)  
On EACH & server, install the **Director WMI Provider** (DirectorWMIProvider\_x64.msi for 64bit OS's and DirectorWMIProvider\_x86.msi for 32bit OS's). No reboot is needed, it takes effect immediately.

17) Enable WinRM on each & server. In a command prompt run:  
'winrm qc'

18) Configure WinRM with the appropriate permissions:  
ConfigRemoteMgmt.exe /configwinrmuser HEALTHYDesktopDirector.TST /all

**\*\*A NOTE ABOUT CONFIGREMOTEMGMT.EXE\*\***

Ensure you use the latest version you can find. For &/XenDesktop 7.7 the version that comes with it is:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B9.49.58-2BPM-1-300x191.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B9.49.58-2BPM-1.png)

It has been [revised as time](http://support.citrix.com/article/CTX131165) has gone on and numerous bug fixes have been implemented. We implemented a script to update our WinRM so all of our & servers would have the user configured. We didn't include checking, we just assumed if a group was added it would not be added again. With an older version of ConfigRemoteMgmt.exe this was an incorrect assumption and our WinRM SDDL became so large that the command line that ConfigRemoteMgmt.exe generates for winrm.cmd breaks the command line. You see, ConfigRemoteMgmt.exe is also a front end to "**C:\Windows\system32\winrm.cmd**". Here is the command it generates:

<span style="font-family: Courier New, Courier, monospace;">C:\Windows\system32\cmd.exe /c ""C:\Windows\system32\winrm.cmd" set winrm/config/service @{RootSDDL="O:NSG:BAD:P(A;;GA;;;<b>S-1-5-21-38857442-2693285798-3636612711-99999999</b>)(A;;GA;;;BA)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)"}"</span>

The part I bolded is the GUID of the object you pass in the /configwinrmuser. The ConfigRemoteMgmt.exe takes your object and converts it to a GUID and then sets the SDDL using the native Windows tools. The bug in a pervious version of this tool is that it would add another GUID, even if one already existed. So in the buggy version the next command would be this:

<pre class="lang:default decode:true">C:\Windows\system32\cmd.exe /c ""C:\Windows\system32\winrm.cmd" set winrm/config/service @{RootSDDL="O:NSG:BAD:P(A;;GA;;;S-1-5-21-38857442-2693285798-3636612711-99999999)(A;;GA;;;S-1-5-21-38857442-2693285798-3636612711-99999999)(A;;GA;;;BA)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)"}"</pre>

And so on. Eventually, this causes WinRM to fail and it will just hang at this point:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.03.42-2BPM-1-300x82.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.03.42-2BPM-1.png)

To check your WinRM Root SDDL, execute this command:

<pre class="lang:batch decode:true">"C:\Windows\system32\winrm.cmd" get winrm/config/service</pre>

If you have an output similar to this, then you have a problem:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.06.22-2BPM-1-204x300.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.06.22-2BPM-1.png)

Fortunately, it's a super easy fix. If you run the command with a single GUID it all gets reset:

<pre class="lang:batch decode:true">C:\Windows\system32\cmd.exe /c ""C:\Windows\system32\winrm.cmd" set winrm/config/service @{RootSDDL="O:NSG:BAD:P(A;;GA;;;BA)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)"}"</pre>

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.07.59-2BPM-1-300x190.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.07.59-2BPM-1.png)

And now you can execute the ConfigRemoteMgmt.exe command again. The new version of ConfigRemoteMgmt.exe doesn't seem to just 'stack' the new version on top.

**/\*\*A NOTE ABOUT CONFIGREMOTEMGMT.EXE\*\***

Now that you have WinRM configured, Director installed, you login and it works. So you try with a HelpDesk account and it fails:

<div style="clear: both; text-align: center;">
</div>

In order to trouble shoot this, we need to turn logging on for Director. To do so, go into IIS Manager Console, to the '**Director**' Site, and double click on **Application Settings**. Turn on the following "Log." settings:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.22.55-2BPM-1-300x154.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.22.55-2BPM-1.png)

Now, create the folder you specified the log will be captured in and set the following permissions on it:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.24.28-2BPM-1-231x300.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.24.28-2BPM-1.png)

Note: The permissions are %COMPUTERNAME%IIS_USERS

Restart IIS and attempt to login again. You should get a log file generated with content. A failed login had this output:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.28.18-2BPM-1-300x180.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.28.18-2BPM-1.png)

And a succesful login had this output:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.28.05-2BPM-1-300x179.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.28.05-2BPM-1.png)

I highlighted the lines in the successful output that showed the command succeeding and the output from that. So why did this fail? The one account "adtest91" is setup as a 'help desk' style account and has 'custom permissions'; essentially 'View-only' but with some categories removed altogether. Could this be permissions based? The odd thing is DesktopDirector 2.1 works without issue with this exact same configuration.

Fortunately, Citrix tries to make this easy to troubleshoot. I will login to the ZDC and start a Citrix PowerShell session with "adtest91" and run the command it supposedly fails on:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.33.57-2BPM-1-300x165.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.33.57-2BPM-1.png)

Well, at least it appears consistent. So, apparently, we need to get this command working. My first try at troubleshooting was to login to the AppCenter console, go to Administrators and get properties on this account, and change it's 'Privileges' from **Custom** to **Full**:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.36.17-2BPM-1-300x200.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.36.17-2BPM-1.png)

When I attempted to login it worked! So it's definitely a permission issue. I went back to Powershell and executed the same command:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.42.10-2BPM-1-300x91.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.42.10-2BPM-1.png)

Success!

So if the Powershell command specified in the log file works then 'Director' should work, and so far that is what it looks like. Now I wanted to narrow down \*which\* permission does this as we do not want our Help Desk to have full control over the farm. The, very obvious, first thing that stood out was:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.45.05-2BPM-1-300x147.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.45.05-2BPM-1.png)

"**Edit Zone Settings**." We are having issues enumerating Zones, perhaps this setting enables you to \*read\* zones? I enabled this setting and retested:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.46.58-2BPM-1-300x38.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.46.58-2BPM-1.png)

Success! And our 'Help Desk' account can now login to Director.

We don't really want Help Desk to have the ability to 'Edit' Zones but I don't see a 'Read-only' permission so we may have to live with it.

So now we've logged into Director and we enumerate an account but we get this message:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.49.51-2BPM-1-300x103.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.49.51-2BPM-1.png)

Cannot retrieve the data. Cannot find the referenced object. View Director server event logs for further information.

The IIS logging shows:

<pre class="lang:default decode:true ">01/14/2016 08:42:30.5003 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] <<<<<<<< WMI SELECT Query will be routed through WinRM Connector >>>>>>>>>
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] GetConnector called. connectionAddress = '(http://wsctxapp303t.healthy.bewell.ca:5985/wsman,6.1.7601)'
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Setting 'Connector.WinRM.Timeout' has value '6000'
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Setting 'Connector.WinRM.Ports' has value '5985'
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] New connector created for connectionAddress = '(http://wsctxapp303t.healthy.bewell.ca:5985/wsman,6.1.7601)'
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] GetConnector returning ...
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] WinRmWqlQuery called
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] query: SELECT * FROM Citrix_Application WHERE SessionId='5'
01/14/2016 08:42:30.5159 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] wqlUri: http://schemas.microsoft.com/wbem/wsman/1/wmi/root/citrix/director/*
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] GetSessions(serverName,sessionId) returning
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] Marking the Connector as active by removing it from failed connectors list
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] GetConnector called. connectionAddress = '(net.tcp://wsctxzdc301t.healthy.bewell.ca:2513/Citrix/&Commands,)'
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] GetConnector returning ...
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] GetApplications(browserNames) called
01/14/2016 08:42:30.5159 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] PowerShell equivalent: Get-XAApplication-BrowserName Philips PACS Enterprise - CHI 
01/14/2016 08:42:30.5316 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] WinRMWqlQueryExecute called
01/14/2016 08:42:30.5316 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] WinRMConnector.HostWithPort : http://wsctxapp303t.healthy.bewell.ca:5985/wsman
01/14/2016 08:42:30.5316 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] GetApplications(browserNames) returning
01/14/2016 08:42:30.5316 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] Marking the Connector as active by removing it from failed connectors list
01/14/2016 08:42:30.5316 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] XAConsoleDao GetSessionFolderPaths(string farmId, string machineId, string sessionId) returning...
01/14/2016 08:42:30.5472 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] WinRmWqlQuery: COMException caught: message is: An internal error occurred. (Exception from HRESULT: 0x8007054F) , errorCode is: -2147023537 
01/14/2016 08:42:30.5472 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] XAConsoleDao GetAuthorizationData() returning
01/14/2016 08:42:30.5472 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] XAConsoleDao GetUserSessionActionAuthorizationAndAvailabilityData(string farmId, string machineId, string sessionId) returning
01/14/2016 08:42:30.5472 : [t:8, s:tnccy45w1lfhxwkzaq0m0geh] EXIT: GetIMASessionActionAuthorizationAndAvailabilityData service returning
01/14/2016 08:42:30.5472 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] RecordEvent called
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] ShouldSuppressEvent called
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Recording last logged time for event 4
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] ShouldSuppressEvent returning: False
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] RecordEvent returning
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Dmc Service error 
 Citrix.Dmc.Common.ConnectorException: Exception of type 'Citrix.Dmc.Common.NotFoundException' was thrown. -</script> Citrix.Dmc.Common.NotFoundException: Exception of type 'Citrix.Dmc.Common.NotFoundException' was thrown.
   at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQueryExecute(String query, String wqlUri)
   at Citrix.Dmc.Connector.WinRMQueryThrottle.ExecuteQuery(String query, String wqlUri, Int32 timeout, Nullable`1 refreshTimeoutInSec, WinRMQueryInvoker invoker)
   at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQuery(String query, String wqlUri, Nullable`1 refreshIntervalInSec)
   at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQuery(String query, String wqlUri)
   at Citrix.Dmc.Common.ConnectorWrapper`1.Invoke[TReturn](Func`2 method)
   --- End of inner exception stack trace ---
   at Citrix.Dmc.Common.ConnectorWrapper`1.Invoke[TReturn](Func`2 method)
   at Citrix.Dmc.WebService.Wmi.WinRMConnectorQuery.ExecuteSelectQuery(String resourceUri, String className, String[] attributes, String whereClause, Nullable`1 timeoutInSec)
   at Citrix.Dmc.WebService.Wmi.VDAMachineDao.ExecuteSelectQuery(String resourceUri, String className, String[] attributes, String whereClause, Nullable`1 timeoutInSec)
   at Citrix.Dmc.WebService.Wmi.VDAMachineDao.GetRunningApplicationsData(String sessionId)
   at Citrix.Dmc.WebService.XAConsoleDao.GetRunningApplicationsData(String farmId, String machineId, String sessionId, Boolean detailed)
   at Citrix.Dmc.WebService.ConsoleService.GetRunningIMAApplicationsData(String farmId, String machineId, String sessionId, Boolean detailed)
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Fault mapper Convert method called
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Service exception is mapped to fault error code 104 
01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Fault mapper Convert method returning
01/14/2016 08:42:30.5784 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] DmcWebErrorHandler channel level error 
 System.ServiceModel.FaultException`1[Citrix.Dmc.WebService.DmcServiceFault]: The creator of this fault did not specify a Reason. (Fault Detail is equal to Citrix.Dmc.WebService.DmcServiceFault).
Citrix.Dmc.Common.NotFoundException: Exception of type 'Citrix.Dmc.Common.NotFoundException' was thrown. at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQueryExecute(String query, String wqlUri) at Citrix.Dmc.Connector.WinRMQueryThrottle.ExecuteQuery(String query, String wqlUri, Int32 timeout, Nullable`1 refreshTimeoutInSec, WinRMQueryInvoker invoker) at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQuery(String query, String wqlUri, Nullable`1 refreshIntervalInSec) at Citrix.Dmc.Connector.WinRMConnector.WinRMWqlQuery(String query, String wqlUri) at Citrix.Dmc.Common.ConnectorWrapper`1.Invoke[TReturn](Func`2 method) --- End of inner exception stack trace --- at Citrix.Dmc.Common.ConnectorWrapper`1.Invoke[TReturn](Func`2 method) at Citrix.Dmc.WebService.Wmi.WinRMConnectorQuery.ExecuteSelectQuery(String resourceUri, String className, String[] attributes, String whereClause, Nullable`1 timeoutInSec) at Citrix.Dmc.WebService.Wmi.VDAMachineDao.ExecuteSelectQuery(String resourceUri, String className, String[] attributes, String whereClause, Nullable`1 timeoutInSec) at Citrix.Dmc.WebService.Wmi.VDAMachineDao.GetRunningApplicationsData(String sessionId) at Citrix.Dmc.WebService.XAConsoleDao.GetRunningApplicationsData(String farmId, String machineId, String sessionId, Boolean detailed) at Citrix.Dmc.WebService.ConsoleService.GetRunningIMAApplicationsData(String farmId, String machineId, String sessionId, Boolean detailed) 01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Fault mapper Convert method called 01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Service exception is mapped to fault error code 104 01/14/2016 08:42:30.5628 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] Fault mapper Convert method returning 01/14/2016 08:42:30.5784 : [t:9, s:tnccy45w1lfhxwkzaq0m0geh] DmcWebErrorHandler channel level error System.ServiceModel.FaultException`1[Citrix.Dmc.WebService.DmcServiceFault]: The creator of this fault did not specify a Reason. (Fault Detail is equal to Citrix.Dmc.WebService.DmcServiceFault). ]]></pre>

And the event log shows:

[<img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.52.17-2BPM-1-300x167.png" border="0" />](/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-13-2Bat-2B10.52.17-2BPM-1.png)

The requested data could not be found in the data 'The virtual desktop via WinRM service reported an exception. See the event log for more information.' ('http://WSCTXZDC301T.healthy.bewell.ca:5985/wsman').

<span style="font-family: Courier New, Courier, monospace; font-size: x-small;">User: 'HEALTHYadtest91'</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;"><br /> </span><span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Console operation: 'Retrieving running application details for IMA Session...'</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;"><br /> </span><span style="font-family: Courier New, Courier, monospace; font-size: x-small;">Additional information:</span>  
<span style="font-family: Courier New, Courier, monospace; font-size: x-small;"><br /> </span><span style="font-family: Courier New, Courier, monospace; font-size: x-small;">'Exception of type 'Citrix.Dmc.Common.NotFoundException' was thrown.'</span>

When I got this message with my 'Administrator' account I installed the Director WMI Provider and it started working. But with this 'limited' user account I was getting an this error. Amazingly, this error goes away as soon as a user logs in to Citrix Director webpage, **who would also be a local administrator on the Director server**. After searching and searching and troubleshooting and scanning and trying various things to get this working, the only thing that would work was to grant our Director group admin privileges on the Director web server. I [finally stumbled across another article](https://support.software.dell.com/kb/154066) that reaffirmed that the only solution is to have the users be local admins on the web server.

<https://support.software.dell.com/kb/154066>

To test WinRM the following command can be used:

<table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
  <tr>
    <td style="text-align: center;">
      <a style="margin-left: auto; margin-right: auto;" href="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-15-2Bat-2B2.46.51-2BPM-1.png"><img src="/wp-content/uploads/2016/01/Screen-2BShot-2B2016-01-15-2Bat-2B2.46.51-2BPM-1-300x149.png" border="0" /></a>
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      winrm get winrm/config -r:%server%    - Non-administrator user gets access is denied making WinRM queries
    </td>
  </tr>
</table>

This error, though, is only if you set Connector.WinRM.Identity as 'User' and those users are not Administrators.  The fix, is to put your users into a group and make them a local admin on the Director server.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
