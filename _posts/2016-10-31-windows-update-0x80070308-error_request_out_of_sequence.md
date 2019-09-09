---
id: 1789
title: Windows Update - 0x80070308 ERROR_REQUEST_OUT_OF_SEQUENCE
date: 2016-10-31T14:19:40-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1789
permalink: /2016/10/31/windows-update-0x80070308-error_request_out_of_sequence/
image: /wp-content/uploads/2016/10/windowsupdatebug9-1.png
categories:
  - Blog
tags:
  - 2008R2
  - procmon
  - Registry
  - Windows Update
---
We are getting this error when trying to install KB3172605, which was re-released in September of this year.  This patch was originally installed in August without issue but the re-release fails to install.  The WindowsUpdate.log reports:

<pre class="lang:default decode:true ">2016-10-31	11:24:28:489	1384	1748	Handler	:: START ::  Handler: CBS Install
2016-10-31	11:24:28:489	1384	1748	Handler	:::::::::
2016-10-31	11:24:28:505	1384	1748	Handler	Starting install of CBS update E606841C-05C3-42C7-BDD4-3777914953B8
2016-10-31	11:24:28:598	1384	1748	Handler	CBS package identity: Package_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4
2016-10-31	11:24:28:598	1384	1748	Handler	Installing self-contained with source=C:\WINDOWS\SoftwareDistribution\Download\596c974a88038a00415c5357d7c4cd33\windows6.1-kb3172605-x64.cab, workingdir=C:\WINDOWS\SoftwareDistribution\Download\596c974a88038a00415c5357d7c4cd33\inst
2016-10-31	11:24:33:411	1524	17ac	Report	REPORT EVENT: {0F12C265-4B12-47A1-848B-C7A999AF291B}	2016-10-31 11:24:28:411-0600	1	181	101	{549A598B-FECB-4D4A-A636-64B7643AC2F3}	501	0	wusa	Success	Content Install	Installation Started: Windows successfully started the following update: Update for Windows (KB3172605)
2016-10-31	11:24:48:739	1524	17ac	Report	Uploading 5 events using cached cookie, reporting URL = http://wswsus02.healthy.bewell.ca/ReportingWebService/ReportingWebService.asmx
2016-10-31	11:24:48:755	1524	17ac	Report	Reporter successfully uploaded 5 events.
2016-10-31	11:25:11:755	1384	1174	Handler	FATAL: CBS called Error with 0x80070308, 
2016-10-31	11:25:13:645	1384	1748	Handler	FATAL: Completed install of CBS update with type=0, requiresReboot=0, installerError=1, hr=0x80070308
2016-10-31	11:25:13:661	1384	1748	Handler	:::::::::
2016-10-31	11:25:13:661	1384	1748	Handler	::  END  ::  Handler: CBS Install
</pre>

Examining the CBS.LOG for more information has revealed the following:

<pre class="lang:default decode:true ">Install (5): flags: 0 tlc: [b964dad2cfda55b03ab17b3d6225bf2a, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_215_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-371_neutral_LDR" ncdata: [l:2{1}]"4") thumbprint: (null)
  Install (5): flags: 0 tlc: [09890cfcc80e3f28e03127e4e92b70c1, Version = 6.1.7601.23446, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_216_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-372_neutral_LDR" ncdata: [l:2{1}]"4") thumbprint: (null)
  Install (5): flags: 0 tlc: [55fc5c26ee33f7af39b6e955951d9951, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: 
2016-10-31 11:25:10, Info                  CSI    [l:162{81}]"Package_217_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-373_neutral_LDR" ncdata: [l:2{1}]"4") thumbprint: (null)
  Install (5): flags: 0 tlc: [1dd595ccef4ec3f57c911ba6e7f0e3e5, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_218_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-374_neutral_LDR" ncdata: [l:4{2}]"17") thumbprint: (null)
  Install (5): flags: 0 tlc: [313e293b6b18f5739dfa4f583b093f94, Version = 6.1.7601.23468, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_219_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-375_neutral_LDR" ncdata: [l:2{1}]"4") thumbprint: (null)
  Install (5): flags: 0 tlc: [f31f881f4fed9ddc8c471d9dd7a7faa9, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_220_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-376_neutral_LDR" ncdata: [l:2{1}]"5") thumbprint: (null)
  Install (5): flags: 0 tlc: [3263a2d6cc50fdfaa752bf29654af055, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_221_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-377_neutral_LDR" ncdata: [l:2{1}]"5") thumbprint: (null)
  Install (
2016-10-31 11:25:10, Info                  CSI    5): flags: 0 tlc: [0056e69823ce1bfb38fe27ada797736f, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_222_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-379_neutral_LDR" ncdata: [l:4{2}]"16") thumbprint: (null)
2016-10-31 11:25:11, Info                  CSI    000000eb Over-removing a TLC, s/b internal error 0e471cf709070f76ea5797942bb36096, Version = 6.1.7601.23455, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral

2016-10-31 11:25:11, Error                 CSI    000000ec@2016/10/31:17:25:11.317 (F) d:\win7sp1_gdr\base\wcp\componentstore\analysis.cpp(1186): Error STATUS_REQUEST_OUT_OF_SEQUENCE originated in function CServicingFamilyEntries::RemoveInstalledTlc expression: (null)
[gle=0x80004005]
2016-10-31 11:25:11, Error                 CSI    000000ed (F) STATUS_REQUEST_OUT_OF_SEQUENCE #813471# from CTransactionAnalysis::GenerateServicingFamilies(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000ee (F) STATUS_REQUEST_OUT_OF_SEQUENCE #785758# from CCSDirectTransaction::PerformChangeAnalysis(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000ef (F) STATUS_REQUEST_OUT_OF_SEQUENCE #785757# from CCSDirectTransaction::PrepareForCommit(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f0 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #785756# from CCSDirectTransaction::ExamineTransaction(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f1 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #785755# from CCSDirectTransaction_IRtlTransaction::ExamineTransaction(...)[gle=0xd000042a]
2016-10-31 11:25:11, Info                  CSI    000000f2 Over-removing a TLC, s/b internal error 0e471cf709070f76ea5797942bb36096, Version = 6.1.7601.23455, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral

2016-10-31 11:25:11, Error                 CSI    000000f3@2016/10/31:17:25:11.473 (F) d:\win7sp1_gdr\base\wcp\componentstore\analysis.cpp(1186): Error STATUS_REQUEST_OUT_OF_SEQUENCE originated in function CServicingFamilyEntries::RemoveInstalledTlc expression: (null)
[gle=0x80004005]
2016-10-31 11:25:11, Error                 CSI    000000f4 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #821127# from CTransactionAnalysis::GenerateServicingFamilies(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f5 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #820325# from CCSDirectTransaction::PerformChangeAnalysis(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f6 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #820324# from CCSDirectTransaction::PrepareForCommit(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f7 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #820323# from CCSDirectTransaction::GenerateComponentChangeList(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f8 (F) STATUS_REQUEST_OUT_OF_SEQUENCE #820322# from Windows::COM::CPendingTransaction::ExtractInformationFromRtlTransaction(...)[gle=0xd000042a]
2016-10-31 11:25:11, Error                 CSI    000000f9 (F) HRESULT_FROM_WIN32(ERROR_REQUEST_OUT_OF_SEQUENCE) #779736# from Windows::COM::CPendingTransaction::IStorePendingTransaction_Analyze(...)[gle=0x80070308]
2016-10-31 11:25:11, Error                 CSI    000000fa (F) HRESULT_FROM_WIN32(ERROR_REQUEST_OUT_OF_SEQUENCE) #777134# from Windows::ServicingAPI::CCSITransaction::ICSITransaction_Commit(Flags = 102 (0x00000066), pSink = NULL, disp = 0, coldpatching = FALSE)[gle=0x80070308]
2016-10-31 11:25:11, Error                 CSI    000000fb (F) HRESULT_FROM_WIN32(ERROR_REQUEST_OUT_OF_SEQUENCE) #777133# 872079 us from Windows::ServicingAPI::CCSITransaction_ICSITransaction::Commit(flags = 0x00000066, pSink = NULL, disp = 0)
[gle=0x80070308]
2016-10-31 11:25:11, Info                  CBS    Setting ExecuteState key to: ExecuteStateNone
2016-10-31 11:25:11, Info                  CBS    Setting RollbackFailed flag to 0
2016-10-31 11:25:11, Info                  CBS    Clearing HangDetect value
2016-10-31 11:25:11, Info                  CBS    Saved last global progress. Current: 0, Limit: 1, ExecuteState: ExecuteStateNone
2016-10-31 11:25:11, Error                 CBS    Exec: Failed to commit CSI transaction to execute changes. [HRESULT = 0x80070308 - ERROR_REQUEST_OUT_OF_SEQUENCE]
2016-10-31 11:25:11, Info                  CSI    000000fc@2016/10/31:17:25:11.536 CSI Transaction @0x23adf60 destroyed
2016-10-31 11:25:11, Info                  CBS    Perf: InstallUninstallChain complete.
2016-10-31 11:25:11, Info                  CBS    Failed to execute execution chain. [HRESULT = 0x80070308 - ERROR_REQUEST_OUT_OF_SEQUENCE]
2016-10-31 11:25:11, Error                 CBS    Failed to process single phase execution. [HRESULT = 0x80070308 - ERROR_REQUEST_OUT_OF_SEQUENCE]
2016-10-31 11:25:11, Info                  CBS    WER: Generating failure report for package: Package_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4, status: 0x80070308, failure source: Execute, start state: Staged, target state: Installed, client id: WindowsUpdateAgent
2016-10-31 11:25:11, Info                  CBS    Failed to query DisableWerReporting flag.  Assuming not set... [HRESULT = 0x80070002 - ERROR_FILE_NOT_FOUND]
2016-10-31 11:25:11, Info                  CBS    Failed to add %windir%\winsxs\pending.xml to WER report because it is missing.  Continuing without it...
2016-10-31 11:25:11, Info                  CBS    Failed to add %windir%\winsxs\pending.xml.bad to WER report because it is missing.  Continuing without it...
</pre>

The error starts here:

<pre class="lang:default decode:true ">Install (5): flags: 0 tlc: [3263a2d6cc50fdfaa752bf29654af055, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_221_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-377_neutral_LDR" ncdata: [l:2{1}]"5") thumbprint: (null)
  Install (
2016-10-31 11:25:10, Info                  CSI    5): flags: 0 tlc: [0056e69823ce1bfb38fe27ada797736f, Version = 6.1.7601.23539, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:162{81}]"Package_222_for_KB3172605~31bf3856ad364e35~amd64~~6.1.1.4.3172605-379_neutral_LDR" ncdata: [l:4{2}]"16") thumbprint: (null)
2016-10-31 11:25:11, Info                  CSI    000000eb Over-removing a TLC, s/b internal error 0e471cf709070f76ea5797942bb36096, Version = 6.1.7601.23455, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral

2016-10-31 11:25:11, Error                 CSI    000000ec@2016/10/31:17:25:11.317 (F) d:\win7sp1_gdr\base\wcp\componentstore\analysis.cpp(1186): Error STATUS_REQUEST_OUT_OF_SEQUENCE originated in function CServicingFamilyEntries::RemoveInstalledTlc expression: (null)
[gle=0x80004005]
2016-10-31 11:25:11, Error                 CSI    000000ed (F) STATUS_REQUEST_OUT_OF_SEQUENCE #813471# from CTransactionAnalysis::GenerateServicingFamilies(...)[gle=0xd000042a]
</pre>

One of the nice things about Windows Update is it kicks up Windows Error Reporting immediately when it detects an error.  So if you run procmon.exe you can find when the error occurs and work backwards only a little bit to, usually, find why it crashed.

In this case I ran procmon while I tried installing this update, went to 11:25:10 and did a search for "Windows Error Reporting"

<img class="aligncenter size-large wp-image-1790" src="/wp-content/uploads/2016/10/windowsupdatebug1-1024x483.png" alt="windowsupdatebug1" width="1024" height="483" srcset="/wp-content/uploads/2016/10/windowsupdatebug1-1024x483.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug1-300x141.png 300w, /wp-content/uploads/2016/10/windowsupdatebug1-768x362.png 768w, /wp-content/uploads/2016/10/windowsupdatebug1.png 1267w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

The actual Windows Error Reporting kicked in at the check to see if it is disabled (DisableWerReporting).  The last key it read before it crashed was:

"HKLM\COMPONENTS\DerivedData\Components\amd64\_0e471cf709070f76ea5797942bb36096\_31bf3856ad364e35\_6.1.7601.23455\_none_9b9ebc8fa6659c8e"

If I open that key in Regedit here is what it looks like:

<img class="aligncenter size-large wp-image-1791" src="/wp-content/uploads/2016/10/windowsupdatebug2-1-1024x147.png" alt="windowsupdatebug2" width="1024" height="147" srcset="/wp-content/uploads/2016/10/windowsupdatebug2-1-1024x147.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug2-1-300x43.png 300w, /wp-content/uploads/2016/10/windowsupdatebug2-1-768x110.png 768w, /wp-content/uploads/2016/10/windowsupdatebug2-1.png 1280w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

And what another key in that same list looks like:

<img class="aligncenter size-large wp-image-1792" src="/wp-content/uploads/2016/10/windowsupdatebug3-1-1024x176.png" alt="windowsupdatebug3" width="1024" height="176" srcset="/wp-content/uploads/2016/10/windowsupdatebug3-1-1024x176.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug3-1-300x52.png 300w, /wp-content/uploads/2016/10/windowsupdatebug3-1-768x132.png 768w, /wp-content/uploads/2016/10/windowsupdatebug3-1.png 1278w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

&nbsp;

It appears the key that is in error is missing some information.  In order to fix this I'm going to another system and try to grab they values needed for identity and S256H.  I don't know how these keys are generated but I'm hoping they are generated by the file it's referring to which \*should\* mean that a same or similar level patched system should have the same files and thus the same generated hash's.

I went to another system and exported the values.  In order to find the proper key, we find it is referenced by the previous key in the procmon trace:  
"amd64\_microsoft-windows-b..environment-windows\_31bf3856ad364e35\_6.1.7601.23455\_none_c7bdc8a2bca7..."

The key path was present in both systems because they were at the same patch level but the look of the key is different because of GUIDs are generated:

<div id="attachment_1793" style="width: 1034px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1793" class="wp-image-1793 size-large" src="/wp-content/uploads/2016/10/windowsupdatebug10-1-1024x195.png" alt="windowsupdatebug10" width="1024" height="195" srcset="/wp-content/uploads/2016/10/windowsupdatebug10-1-1024x195.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug10-1-300x57.png 300w, /wp-content/uploads/2016/10/windowsupdatebug10-1-768x146.png 768w, /wp-content/uploads/2016/10/windowsupdatebug10-1.png 1253w" sizes="(max-width: 1024px) 100vw, 1024px" /></p> 
  
  <p id="caption-attachment-1793" class="wp-caption-text">
    Working system - Notice the highlight is different
  </p>
</div>

By tracing back in the working system I exported the key that "c!" is referencing.

<div id="attachment_1794" style="width: 1034px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-1794" class="size-large wp-image-1794" src="/wp-content/uploads/2016/10/windowsupdatebug11-1-1024x194.png" alt="Working System has S256H, identity keys" width="1024" height="194" srcset="/wp-content/uploads/2016/10/windowsupdatebug11-1-1024x194.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug11-1-300x57.png 300w, /wp-content/uploads/2016/10/windowsupdatebug11-1-768x145.png 768w, /wp-content/uploads/2016/10/windowsupdatebug11-1.png 1248w" sizes="(max-width: 1024px) 100vw, 1024px" /></p> 
  
  <p id="caption-attachment-1794" class="wp-caption-text">
    Working System has S256H, identity keys
  </p>
</div>

I exported out the key from the working system and had to edit it so the GUID matched the broken key's values.  I took the 'working' registry:

<pre class="lang:default decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\COMPONENTS\DerivedData\Components\amd64_c4ebacc53555cd9cdedd693c10e6175a_31bf3856ad364e35_6.1.7601.23455_none_8459b72d1e2f2700]
"S256H"=hex:0f,a1,b1,ae,e7,ec,04,9f,3f,0f,1a,3c,2d,ed,14,03,3d,0e,1f,e4,20,cd,\
  6d,d3,b6,07,8c,2f,80,cf,2c,c8
"identity"=hex:63,34,65,62,61,63,63,35,33,35,35,35,63,64,39,63,64,65,64,64,36,\
  39,33,63,31,30,65,36,31,37,35,61,2c,20,43,75,6c,74,75,72,65,3d,6e,65,75,74,\
  72,61,6c,2c,20,56,65,72,73,69,6f,6e,3d,36,2e,31,2e,37,36,30,31,2e,32,33,34,\
  35,35,2c,20,50,75,62,6c,69,63,4b,65,79,54,6f,6b,65,6e,3d,33,31,62,66,33,38,\
  35,36,61,64,33,36,34,65,33,35,2c,20,50,72,6f,63,65,73,73,6f,72,41,72,63,68,\
  69,74,65,63,74,75,72,65,3d,61,6d,64,36,34,2c,20,76,65,72,73,69,6f,6e,53,63,\
  6f,70,65,3d,4e,6f,6e,53,78,53
"ClosureFlags"=dword:00000003
"c!c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_8459b72d1e2f2700"=hex:

</pre>

And the broken key:

<pre class="lang:default decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\COMPONENTS\DerivedData\Components\amd64_0e471cf709070f76ea5797942bb36096_31bf3856ad364e35_6.1.7601.23455_none_9b9ebc8fa6659c8e]
"ClosureFlags"=dword:00000003</pre>

I added the S256H and identity values to the 'broken' key:

<pre class="lang:default decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\COMPONENTS\DerivedData\Components\amd64_0e471cf709070f76ea5797942bb36096_31bf3856ad364e35_6.1.7601.23455_none_9b9ebc8fa6659c8e]
"ClosureFlags"=dword:00000003
"S256H"=hex:0f,a1,b1,ae,e7,ec,04,9f,3f,0f,1a,3c,2d,ed,14,03,3d,0e,1f,e4,20,cd,\
  6d,d3,b6,07,8c,2f,80,cf,2c,c8
"identity"=hex:63,34,65,62,61,63,63,35,33,35,35,35,63,64,39,63,64,65,64,64,36,\
  39,33,63,31,30,65,36,31,37,35,61,2c,20,43,75,6c,74,75,72,65,3d,6e,65,75,74,\
  72,61,6c,2c,20,56,65,72,73,69,6f,6e,3d,36,2e,31,2e,37,36,30,31,2e,32,33,34,\
  35,35,2c,20,50,75,62,6c,69,63,4b,65,79,54,6f,6b,65,6e,3d,33,31,62,66,33,38,\
  35,36,61,64,33,36,34,65,33,35,2c,20,50,72,6f,63,65,73,73,6f,72,41,72,63,68,\
  69,74,65,63,74,75,72,65,3d,61,6d,64,36,34,2c,20,76,65,72,73,69,6f,6e,53,63,\
  6f,70,65,3d,4e,6f,6e,53,78,53</pre>

And then added the last "c!" line by following these next steps:

  1. copied the line out:  
    "c!c4ebacc5355..93c10e6175a\_31bf3856ad364e35\_6.1.7601.23455_8459b72d1e2f2700&8243;=hex:
  2. Pasted the value from the registry "key" underneath with the "broken" value: <pre class="lang:default decode:true">"c!c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_8459b72d1e2f2700"=hex:
   c4ebacc53555cd9cdedd693c10e6175a_31bf3856ad364e35_6.1.7601.23455_none_8459b72d1e2f2700
   0e471cf709070f76ea5797942bb36096_31bf3856ad364e35_6.1.7601.23455_none_9b9ebc8fa6659c8e</pre>

  3. Starting at the ellipse, counted the number of characters from the "55.." to "93&8242; underneath and created the value: <pre class="lang:default decode:true">"c!c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_8459b72d1e2f2700"=hex:
   c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_none_8459b72d1e2f2700
   0e471cf7090..7942bb36096_31bf3856ad364e35_6.1.7601.23455_none_9b9ebc8fa6659c8e</pre>

  4. Lastly, removed the '_none" from the string: <pre class="lang:default decode:true">"c!c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_8459b72d1e2f2700"=hex:
   c4ebacc5355..93c10e6175a_31bf3856ad364e35_6.1.7601.23455_8459b72d1e2f2700
   0e471cf7090..7942bb36096_31bf3856ad364e35_6.1.7601.23455_9b9ebc8fa6659c8e</pre>

  5. I could then add "c!" in front and added it to my 'broken' registry file: <pre class="lang:default decode:true ">Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\COMPONENTS\DerivedData\Components\amd64_0e471cf709070f76ea5797942bb36096_31bf3856ad364e35_6.1.7601.23455_none_9b9ebc8fa6659c8e]
"ClosureFlags"=dword:00000003
"S256H"=hex:0f,a1,b1,ae,e7,ec,04,9f,3f,0f,1a,3c,2d,ed,14,03,3d,0e,1f,e4,20,cd,\
  6d,d3,b6,07,8c,2f,80,cf,2c,c8
"identity"=hex:63,34,65,62,61,63,63,35,33,35,35,35,63,64,39,63,64,65,64,64,36,\
  39,33,63,31,30,65,36,31,37,35,61,2c,20,43,75,6c,74,75,72,65,3d,6e,65,75,74,\
  72,61,6c,2c,20,56,65,72,73,69,6f,6e,3d,36,2e,31,2e,37,36,30,31,2e,32,33,34,\
  35,35,2c,20,50,75,62,6c,69,63,4b,65,79,54,6f,6b,65,6e,3d,33,31,62,66,33,38,\
  35,36,61,64,33,36,34,65,33,35,2c,20,50,72,6f,63,65,73,73,6f,72,41,72,63,68,\
  69,74,65,63,74,75,72,65,3d,61,6d,64,36,34,2c,20,76,65,72,73,69,6f,6e,53,63,\
  6f,70,65,3d,4e,6f,6e,53,78,53
"c!0e471cf7090..7942bb36096_31bf3856ad364e35_6.1.7601.23455_9b9ebc8fa6659c8e"=hex:</pre>
    
    I then tried to install the patch:
    
<img class="aligncenter size-full wp-image-1795" src="/wp-content/uploads/2016/10/windowsupdatebug9-1.png" alt="windowsupdatebug9" width="548" height="383" srcset="/wp-content/uploads/2016/10/windowsupdatebug9-1.png 548w, /wp-content/uploads/2016/10/windowsupdatebug9-1-300x210.png 300w" sizes="(max-width: 548px) 100vw, 548px" /> </li> </ol> 
    
    And it worked!
    
    <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->