---
id: 1577
title: 'Citrix & 6.5 - IMA errors galore, mfcom won't start'
date: 2016-07-04T14:35:44-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1577
permalink: /2016/07/04/citrix-&-6-5-ima-errors-galore-mfcom-wont-start/
categories:
  - Blog
tags:
  - 2008R2
  - Citrix
  - Performance
  - Registry
  - scripting
  - &
---
I've seen this happen a few times now where the "Citrix Independent Management Architecture" (aka IMAService) won't start, erroring with various errors:

<pre class="">3616 - Configuration error: Failed to read the farm name out of the registry on a server configured  to access the Data Store directly.
3609 - Failed to load plugin C:\Program Files (x86)\Citrix\System32\Citrix\IMA\SubSystems\ImaRuntimeSS.dll with error IMA_RESULT_CONFIGURATION_ERROR
3601 - Failed to load initial plugins with error IMA_RESULT_CONFIGURATION_ERROR
4005 - The Citrix Independent Management Architecture (IMA) service is exiting. The Neighborhood (farm name) could not be read from the Data Store or written to the registry.
3609 - Failed to load plugin C:\Program Files (x86)\Citrix\System32\Citrix\IMA\SubSystems\ImaPsSs.dll with error IMA_RESULT_REGISTRY_ERROR
3601 - Failed to load initial plugins with error IMA_RESULT_REGISTRY_ERROR
3609 - Failed to load plugin C:\Program Files (x86)\Citrix\System32\Citrix\IMA\SubSystems\MfSrvSs.dll with error IMA_RESULT_FAILURE
4003 - The Citrix Independent Management Architecture (IMA) service is exiting. The & Server Configuration tool has not been run on this server. Please run  & Server Configuration tool.</pre>

All of these errors appear to be a registry with incorrect permissions configured on the Citrix keys. � Why did these keys get their permissions reset? � I'm unsure. � I DID just install Citrix UPM 5.4 which may reset the keys?

Here is how you fix the permissions (at least, everything I could possibly find):  
1) [Download SetACL.exe](https://helgeklein.com/download/)  
2) Save this file to 'CitrixRegPerms.txt':

<pre class="lang:default decode:true">"machine\SOFTWARE\Wow6432Node\Citrix",4,"D:PAI(A;OICI;KA;;;BA)(A;OICI;KA;;;SY)(A;;KA;;;SY)(A;OICIIO;KA;;;CO)(A;OICI;KR;;;AU)(A;OICI;KA;;;NS)"
"machine\SOFTWARE\Wow6432Node\Citrix\Audio\status",4,"D:AI"
"machine\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Smart Card Hook\C:/Windows/system32/lsass.exe",4,"D:AI"
"machine\SOFTWARE\Wow6432Node\Citrix\CtxHook\AppInit_Dlls\Smart Card Hook\C:/Windows/system32/winlogon.exe",4,"D:AI"
"machine\SOFTWARE\Wow6432Node\Citrix\EUEM\LoggedEvents",4,"D:PAI(A;OICI;KA;;;NS)(A;OICI;KA;;;LS)(A;OICI;KA;;;RD)(A;OICI;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\HdxMediaStreamForFlash\Server",4,"D:AI(A;CI;KA;;;S-1-5-80-1435782392-1812815856-2897313918-4236673578-1819255674)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Data",4,"D:PAI(A;OICI;KA;;;BA)(A;OICI;KA;;;SY)(A;OICI;KA;;;NS)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Datastore",4,"D:AI(A;CI;KA;;;SY)(A;CI;KA;;;NS)(A;CI;KA;;;BA)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\ImaAppSS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\IMAServers",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\LHCDatasource",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\LMS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Logging",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\MFRPC",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Protection",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\RadeDataSource",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\RUNTIME",4,"D:PAI(A;OICI;KA;;;BA)(A;OICI;KA;;;SY)(A;OICI;KA;;;NS)(A;OICI;KR;;;AU)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Status",4,"D:AI(A;CI;KA;;;SY)(A;CI;KA;;;NS)(A;CI;KA;;;BA)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer",4,"D:AI(A;;KA;;;SY)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ActiveDirectoryMgmt",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Admin Subsystem",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Content",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Directory Subsystem",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMA PN",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaAdminSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaAppSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaAppSs",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMADistribution",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaDistSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaExternStorSS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMAFC",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMAGroup",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaGrpSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaMfRpc",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaRpc",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaSrvSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaSrvSs",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaUserSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\ImaWorkerGroup",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMA_AAMS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\IMA_Printer",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\LmsSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\LMS_Subsystem",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MFApp",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MfAppSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MFContentSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MfPnSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MfPrintSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MfSrvSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\MFSrvSs",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Policy",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\RADEAppSS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\RADESessionSS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Remote Access",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\RemoteAccess",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\DynamicStore",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\HostResolver",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\PersistentStore",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\Runtime",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\ServiceLocator",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\SubscriptionManager",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Runtime\ZoneManager",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Sessions",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\DataStoreDriver",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\Loader",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\Message",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\Registrar",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\Support",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\System",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\System\Transport",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Test",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Unknown ComponentSal",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Utilities",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\Utilities\Q",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracer\WinDrvSS",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\Tracing",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\upgrade",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\WorkerGroups",4,"D:PAI(A;;KA;;;LS)(A;;KA;;;NS)(A;;KA;;;BA)(A;;KA;;;SY)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMA\XTE_Started",4,"D:(A;;KA;;;SY)(A;;RC;;;OW)(A;;KA;;;S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759)"
"machine\SOFTWARE\Wow6432Node\Citrix\IMAPrinter",4,"D:AI"
"machine\SOFTWARE\Wow6432Node\Citrix\Install",4,"D:PAI(A;OICI;KA;;;BA)(A;OICI;KA;;;SY)(A;OICI;KR;;;AU)"
"machine\SOFTWARE\Wow6432Node\Citrix\RebootSchedule",4,"D:AI"
"machine\SOFTWARE\Wow6432Node\Citrix\WMIService",4,"D:PAI(A;OICI;KA;;;BA)(A;OICI;KA;;;SY)(A;;KA;;;SY)(A;OICIIO;KA;;;CO)(A;OICI;KR;;;AU)(A;OICI;KA;;;LS)"</pre>

You may� need to identify the local SID for 'NETWORKSERVICE'. � In my example the value is:

<pre class="lang:default decode:true">S-1-5-80-2037085886-1864634726-376116143-1108166061-1304636759</pre>

&nbsp;

You may need to replace your SID for NetworkService with the one from my file above.

Lastly this script will 'fix' the incorrect permissions:

<pre class="lang:batch decode:true">pushd %~dp0
:stop mfcom.exe if it's stuck in 'Starting'
taskkill /im mfcom.exe /f
:backup reg perms
setacl -on "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix" -ot reg -actn list -lst "f:sddl" -bckp BackupRegPerms.txt -rec yes

:restore reg perms
:reset childern permissions so they will inherit if inherit became broken for some reason
setacl -on "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix" -ot reg -actn rstchldrn -rst dacl,sacl
:set local admins as owners on IMA
setacl -on "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Citrix\IMA" -ot reg -actn setowner -ownr "n:Administrators" -rec yes
:restore permissions on Citrix key..
setacl -on "HKEY_LOCAL_MACHINE" -ot reg -actn restore -bckp CitrixRegPerms.txt
:restore NetworkService permisisons on misc Microsoft keys
setacl -on "HKLM\SOFTWARE\Citrix\MSLicensing\ReplicateCache" -ot reg -actn ace -ace "n:NETWORKSERVICE;p:full;m:grant"
setacl -on "HKEY_LOCAL_MACHINE\SOFTWARE\Citrix\MSLicensing\LastUpdateTimeStamp" -ot reg -actn ace -ace "n:NETWORKSERVICE;p:full;m:grant"
popd
</pre>

Done.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->