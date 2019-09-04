---
id: 1086
title: 'IMA Service Fails with the Following Events: 3989, 3634, 3614'
date: 2016-04-25T07:40:44-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/625-autosave-v1/
permalink: /2016/04/25/625-autosave-v1/
---
We have run into an issue where we have Provisioning Service 6.1 and XenApp 6.5 working together. After we update the vDisk (say, for Windows Update) we run through a script that does things like the &#8220;XenApp Prep&#8221; to allow the XenApp 6.5 ready for imaging. It appears that there is a bug in the XenApp Prep that sometimes causes it to not fully get XenApp 6.5 ready for rejoining the farm. The initial symptoms I found were:

Event ID 4003  
&#8220;The Citrix Independent Management Architecture service is exiting. The XenApp Server Configuration tool has not been run on this server.&#8221;

I found this [CTX article](http://support.citrix.com/article/CTX137758) about it, but nothing of it was applicable.

I did procmon traces and I found the following registry keys were missing on the bad system:

[<img src="http://2.bp.blogspot.com/-Ox5vZSYIt4M/UkNGPmdxEeI/AAAAAAAAAYc/utHjsEnKxlA/s320/2.png" border="0" />](http://2.bp.blogspot.com/-Ox5vZSYIt4M/UkNGPmdxEeI/AAAAAAAAAYc/utHjsEnKxlA/s1600/2.png)  
A broken system missing the Status Registry key

[<img src="http://4.bp.blogspot.com/-PKUVY--m4Hs/UkNGQvPv5rI/AAAAAAAAAYk/5hx7JfVaHtc/s320/3.png" border="0" />](http://4.bp.blogspot.com/-PKUVY--m4Hs/UkNGQvPv5rI/AAAAAAAAAYk/5hx7JfVaHtc/s1600/3.png)  
A working system with the Status key. Note Joined is &#8220;0&#8221;

After adding the Status Registry key:

<pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA\Status]
"EnhancedDesktopExperienceConfigured"=dword:00000001
"Provisioned"=dword:00000001
"Ready"=dword:00000001
"Joined"=dword:00000000</pre>

I tried restarting the service and the progress bar got further but then still quit. Procmon showed me this:

[<img src="http://1.bp.blogspot.com/-QOc7ePSqid4/UkNHfHEr1iI/AAAAAAAAAY0/HIkcrX4QUPs/s320/4.png" border="0" />](http://1.bp.blogspot.com/-QOc7ePSqid4/UkNHfHEr1iI/AAAAAAAAAY0/HIkcrX4QUPs/s1600/4.png)

That is ACCESS DENIED when trying to see that registry key. It turns out that the IMAService does not have appropriate permissions to read this key. The Magic Permissions on a working box and what you need to set here looks like this:

[<img src="http://4.bp.blogspot.com/-ikCgcmS39MI/UkNIJHBkqkI/AAAAAAAAAY8/zo4rXGOnmds/s320/5.png" border="0" />](http://4.bp.blogspot.com/-ikCgcmS39MI/UkNIJHBkqkI/AAAAAAAAAY8/zo4rXGOnmds/s1600/5.png)

Notice that none of the permissions are inherited and &#8220;NETWORK SERVICE&#8221; is added with full control to this key. Now when we try and start the Citrix Independent Management Architecture service we get the following errors:

eventid 3989:  
Citrix XenApp failed to connect to the Data Store. ODBC error while connecting to the database: S1000 -> General error: Invalid file dsn &#8221;  
eventid 3636:  
The server running Citrix XenApp failed to connect to the data store. An unknown failure occurred while connecting to the database. Error: IMA\_RESULT\_FAILURE Indirect: 0 Server: DSN file:  
eventid 3615  
The server running Citrix XenApp failed to connect to the Data Store. Error &#8211; IMA\_RESULT\_FAILURE An unknown failure occurred while connecting to the database.  
eventid 3609  
Failed to load plugin C:Program Files (x86)CitrixSystem32CitrixIMASubSystemsImaWorkerGroupSs.dll with error IMA\_RESULT\_FAILURE  
eventid 3601  
Failed to load initial plugins with error IMA\_RESULT\_FAILURE

[To correct these errors the local host cache needs to be rebuilt.](http://support.citrix.com/article/CTX127922) To fix that we need to run:  
Dsmaint recreatelhc  
Dsmaint recreaterade

After doing that, we can start the IMA Service and MFCOM. If instead of IMA Service starting you get the following error message:  
Eventid 4007  
The Citrix Independent Management Architecture (IMA) service is exiting. The DSN File could not be updated with the information retrieved from Group Policy. Error: 80000001h DSN file: mf20.dsn DSN Entry: DATABASE Policy Setting: CTXDS Confirm that the Network Service has write permissions to the DSN File.

Ensure the following registry is populated:  
reg add HKEY\_LOCAL\_MACHINEPE_SOFTWAREWow6432NodeCitrixIMA

/v DataSourceName /t REG_SZ /d &#8220;C:Program Files (x86)CitrixIndependent Management Architecturemf20.dsn&#8221;

<div>
  <div>
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->