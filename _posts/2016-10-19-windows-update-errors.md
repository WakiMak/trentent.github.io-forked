---
id: 1764
title: Windows Update - Errors 80070057, 800736B3, 8024400E
date: 2016-10-19T15:12:25-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1764
permalink: /2016/10/19/windows-update-errors/
categories:
  - Blog
tags:
  - 2008R2
  - procmon
  - Windows Update
---
We started the new patching Microsoft has put forward (cumulative updates) and one of our Citrix vDisks had an issue with it. Windows Update would say that there were no updates available:

<img class="aligncenter size-full wp-image-1765" src="/wp-content/uploads/2016/10/windowsupdatebug.png" alt="windowsupdatebug" width="837" height="317" srcset="/wp-content/uploads/2016/10/windowsupdatebug.png 837w, /wp-content/uploads/2016/10/windowsupdatebug-300x114.png 300w, /wp-content/uploads/2016/10/windowsupdatebug-768x291.png 768w" sizes="(max-width: 837px) 100vw, 837px" /> 

But we very obviously have updates to deploy to it.

When I checked the 'WindowsUpdate.log' I saw the following:

<pre class="lang:default decode:true ">2016-10-19	11:23:47:375	1532	2150	AU	#############
2016-10-19	11:23:47:375	1532	2150	AU	## START ##  AU: Search for updates
2016-10-19	11:23:47:375	1532	2150	AU	#########
2016-10-19	11:23:47:375	1532	2150	AU	<<## SUBMITTED ## AU: Search for updates [CallId = {7BA17D86-EF4B-470B-AF35-2A0271736B9E}]
2016-10-19	11:23:47:375	1532	1f40	Agent	*************
2016-10-19	11:23:47:375	1532	1f40	Agent	** START **  Agent: Finding updates [CallerId = AutomaticUpdates]
2016-10-19	11:23:47:375	1532	1f40	Agent	*********
2016-10-19	11:23:47:375	1532	1f40	Agent	  * Online = Yes; Ignore download priority = No
2016-10-19	11:23:47:375	1532	1f40	Agent	  * Criteria = "IsInstalled=0 and DeploymentAction='Installation' or IsPresent=1 and DeploymentAction='Uninstallation' or IsInstalled=1 and DeploymentAction='Installation' and RebootRequired=1 or IsInstalled=0 and DeploymentAction='Uninstallation' and RebootRequired=1"
2016-10-19	11:23:47:375	1532	1f40	Agent	  * ServiceID = {3DA21691-E39D-4DA6-8A4B-B43877BCB1B7} Managed
2016-10-19	11:23:47:375	1532	1f40	Agent	  * Search Scope = {Machine}
2016-10-19	11:23:47:375	1532	1f40	Setup	Checking for agent SelfUpdate
2016-10-19	11:23:47:375	1532	1f40	Setup	Client version: Core: 7.6.7601.23453  Aux: 7.6.7601.23453
2016-10-19	11:23:47:407	1532	1f40	Misc	Validating signature for C:\Windows\SoftwareDistribution\SelfUpdate\wuident.cab with dwProvFlags 0x00000080:
2016-10-19	11:23:47:407	1532	1f40	Misc	 Microsoft signed: NA
2016-10-19	11:23:47:407	1532	1f40	Misc	WARNING: Cab does not contain correct inner CAB file.
2016-10-19	11:23:47:407	1532	1f40	Misc	Validating signature for C:\Windows\SoftwareDistribution\SelfUpdate\wuident.cab with dwProvFlags 0x00000080:
2016-10-19	11:23:47:422	1532	1f40	Misc	 Microsoft signed: NA
2016-10-19	11:23:47:422	1532	1f40	Setup	Wuident for the managed service is valid but not quorum-signed. Skipping selfupdate.
2016-10-19	11:23:47:422	1532	1f40	Setup	Skipping SelfUpdate check based on the /SKIP directive in wuident
2016-10-19	11:23:47:422	1532	1f40	Setup	SelfUpdate check completed.  SelfUpdate is NOT required.
2016-10-19	11:23:47:610	1532	1f40	PT	+++++++++++  PT: Synchronizing server updates  +++++++++++
2016-10-19	11:23:47:610	1532	1f40	PT	  + ServiceId = {3DA21691-E39D-4DA6-8A4B-B43877BCB1B7}, Server URL = http://wswsus02.healthy.bewell.ca/ClientWebService/client.asmx
2016-10-19	11:23:58:516	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:23:58:547	1532	1f40	Agent	WARNING: Failed to evaluate Installed rule, updateId = {7A967290-323C-4217-A32C-E4AA1574D7D7}.202, hr = 80070057
2016-10-19	11:24:00:172	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:00:204	1532	1f40	Agent	WARNING: Failed to evaluate Installable rule, updateId = {7A967290-323C-4217-A32C-E4AA1574D7D7}.202, hr = 80070057
2016-10-19	11:24:01:844	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:05:891	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:05:938	1532	1f40	Agent	WARNING: Failed to evaluate Installed rule, updateId = {BC84255B-F762-4A50-9C52-F3BED35DAC65}.200, hr = 80070057
2016-10-19	11:24:07:797	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:07:844	1532	1f40	Agent	WARNING: Failed to evaluate Installable rule, updateId = {BC84255B-F762-4A50-9C52-F3BED35DAC65}.200, hr = 80070057
2016-10-19	11:24:09:688	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:14:750	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:14:782	1532	1f40	Agent	WARNING: Failed to evaluate Installed rule, updateId = {8B225A49-634E-4AED-AF0C-81344E9918B9}.200, hr = 80070057
2016-10-19	11:24:16:547	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:16:594	1532	1f40	Agent	WARNING: Failed to evaluate Installable rule, updateId = {8B225A49-634E-4AED-AF0C-81344E9918B9}.200, hr = 80070057
2016-10-19	11:24:18:375	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:22:516	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:22:547	1532	1f40	Agent	WARNING: Failed to evaluate Installed rule, updateId = {2A5A8394-828E-49FC-837D-930A2832EADB}.204, hr = 80070057
2016-10-19	11:24:24:235	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:24:266	1532	1f40	Agent	WARNING: Failed to evaluate Installable rule, updateId = {2A5A8394-828E-49FC-837D-930A2832EADB}.204, hr = 80070057
2016-10-19	11:24:25:953	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:29:094	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:29:125	1532	1f40	Agent	WARNING: Failed to evaluate Installed rule, updateId = {697B6E33-B11F-4BDC-8756-1B84513DE656}.203, hr = 80070057
2016-10-19	11:24:30:813	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:30:844	1532	1f40	Agent	WARNING: Failed to evaluate Installable rule, updateId = {697B6E33-B11F-4BDC-8756-1B84513DE656}.203, hr = 80070057
2016-10-19	11:24:32:547	1532	1f40	Handler	FATAL: UH: 0x80070057: EvaluateApplicability failed in CCbs::EvaluateApplicability
2016-10-19	11:24:32:625	1532	1f40	PT	+++++++++++  PT: Synchronizing extended update info  +++++++++++</pre>

0x80070057 = E_INVALIDARG

The 'CBS' (Component Based Servicing) is reporting an Invalid Argument.  Microsoft keeps a more verbose log of the component based servicing here: C:\Windows\Logs\CBS

This log reported the following:

<pre class="lang:default decode:true">2016-10-19 11:28:37, Info                  CBS    Appl: Selfupdate, Component: x86_microsoft-windows-c..ityclient.resources_31bf3856ad364e35_0.0.0.0_cs-cz_4543013c0104990b (6.1.7601.22843), elevation:2, lower version revision holder: 0.0.0.0
2016-10-19 11:28:37, Info                  CBS    Applicability(ComponentAnalyzerEvaluateSelfUpdate): Component: x86_microsoft-windows-c..ityclient.resources_31bf3856ad364e35_6.1.7601.22843_cs-cz_23d88f3b30536415, elevate: 2, applicable(true/false): 0
2016-10-19 11:28:37, Info                  CSI    00000097@2016/10/19:17:28:37.798 CSI Transaction @0x7a64040 initialized for deployment engine {d16d444c-56d8-11d5-882d-0080c847b195} with flags 00000002 and client id [25]"TI5.30550574_957084783:1/"

2016-10-19 11:28:37, Info                  CSI    00000098@2016/10/19:17:28:37.798 CSI Transaction @0x7a64040 destroyed
2016-10-19 11:28:37, Info                  CBS    Failed to get component version: 6.1.7601.22653  [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to enumerate store versions on component: x86_microsoft-windows-c..ityclient.resources_31bf3856ad364e35_6.1.7601.22843_da-dk_c1126f6226996014 [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to enumerate related component versions on component: x86_microsoft-windows-c..ityclient.resources_31bf3856ad364e35_6.1.7601.22843_da-dk_c1126f6226996014 [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to load current component state [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to find or add the component family [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    ComponentAnalyzerEvaluateSelfUpdate call failed. [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate self update [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate non detect parent update [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate non parent [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    failed to evaluate single applicability [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate applicability [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to get applicability on updates [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Error                 CBS    Failed to call external evaluate applicability on package: Package_49_for_KB3003743~31bf3856ad364e35~amd64~~6.1.1.1, Update: Trigger_1 [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate external applicability for package update: 3003743-498_neutral_PACKAGE [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Error                 CBS    Failed to call external evaluate applicability on package: Package_for_KB3003743_SP1~31bf3856ad364e35~amd64~~6.1.1.1, Update: 3003743-498_neutral_PACKAGE [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Failed to evaluate external applicability for package update: 3003743-594_neutral_PACKAGE [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Error                 CBS    Failed to call external evaluate applicability on package: Package_for_KB3003743~31bf3856ad364e35~amd64~~6.1.1.1, Update: 3003743-594_neutral_PACKAGE [HRESULT = 0x80070057 - E_INVALIDARG]
2016-10-19 11:28:37, Info                  CBS    Session: 30550574_974273603 initialized by client WindowsUpdateAgent.
2016-10-19 11:28:37, Info                  CBS    Read out cached package applicability for package: Package_for_KB2846960~31bf3856ad364e35~amd64~~6.1.1.3, ApplicableState: 112, CurrentState:112
2016-10-19 11:28:37, Info                  CBS    Session: 30550574_974429865 initialized by client WindowsUpdateAgent.
2016-10-19 11:28:37, Info                  CBS    Read out cached package applicability for package: Package_for_KB3084135~31bf3856ad364e35~amd64~~6.1.1.0, ApplicableState: 112, CurrentState:112
2016-10-19 11:28:37, Info                  CBS    Session: 30550574_974429866 initialized by client WindowsUpdateAgent.
2016-10-19 11:28:37, Info                  CBS    Read out cached package applicability for package: Package_for_KB2884256~31bf3856ad364e35~amd64~~6.1.1.1, ApplicableState: 112, CurrentState:112</pre>

An error appeared to occur at this point (there were a few):

**Failed to get component version**: 6.1.7601.22653  
**Failed to enumerate store versions on component**: x86\_microsoft-windows-c..ityclient.resources\_31bf3856ad364e35\_6.1.7601.22843\_da-dk_c1126f6226996014  
**Failed to enumerate related component versions on component**: x86\_microsoft-windows-c..ityclient.resources\_31bf3856ad364e35\_6.1.7601.22843\_da-dk_c1126f6226996014

&nbsp;

The standard MS method of troubleshooting Windows Update is to run the 'CheckSUR' utility.  This was the result of running that tool:

<img class="aligncenter size-full wp-image-1766" src="/wp-content/uploads/2016/10/windowsupdatebug2.png" alt="windowsupdatebug2" width="334" height="341" srcset="/wp-content/uploads/2016/10/windowsupdatebug2.png 334w, /wp-content/uploads/2016/10/windowsupdatebug2-294x300.png 294w, /wp-content/uploads/2016/10/windowsupdatebug2-50x50.png 50w" sizes="(max-width: 334px) 100vw, 334px" /> 

No errors detected.  Awesome.  So I have a problem but this tool reports everything is peachy.

So when I looked at Windows Update I saw numerous 'failed' updates.

<img class="aligncenter size-full wp-image-1767" src="/wp-content/uploads/2016/10/windowsupdatebug3.png" alt="windowsupdatebug3" width="985" height="652" srcset="/wp-content/uploads/2016/10/windowsupdatebug3.png 985w, /wp-content/uploads/2016/10/windowsupdatebug3-300x199.png 300w, /wp-content/uploads/2016/10/windowsupdatebug3-768x508.png 768w" sizes="(max-width: 985px) 100vw, 985px" /> 

I took one that was failed and downloaded it ([KB3177467](https://support.microsoft.com/en-ca/kb/3177467)) and attempted to manually install it.

This time I got a different error:

<pre class="lang:default decode:true">2016-10-19	14:23:56:231	8904	1834	Handler	:: START ::  Handler: CBS Install
2016-10-19	14:23:56:231	8904	1834	Handler	:::::::::
2016-10-19	14:23:56:231	8904	1834	Handler	Starting install of CBS update 9F6C41F5-6D0F-454F-9380-CBF75E6A1CBF
2016-10-19	14:23:56:231	8904	1834	Handler	CBS package identity: Package_for_KB3177467~31bf3856ad364e35~amd64~~6.1.1.1
2016-10-19	14:23:56:231	8904	1834	Handler	Installing self-contained with source=C:\Windows\SoftwareDistribution\Download\db0ec1ac1c6e6b90b22e49fa1c7d8216\windows6.1-kb3177467-x64.cab, workingdir=C:\Windows\SoftwareDistribution\Download\db0ec1ac1c6e6b90b22e49fa1c7d8216\inst
2016-10-19	14:24:01:200	1532	aa4	Report	REPORT EVENT: {CA27992C-3791-43CF-8ED3-18B05939AB84}	2016-10-19 14:23:56:200-0600	1	181	101	{37EA312C-2424-4158-B30F-E614AA28DA4A}	501	0	wusa	Success	Content Install	Installation Started: Windows successfully started the following update: Update for Windows (KB3177467)
2016-10-19	14:24:02:590	8904	18a8	Handler	FATAL: CBS called Error with 0x800736b3, 
2016-10-19	14:24:02:653	8904	1834	Handler	FATAL: Completed install of CBS update with type=0, requiresReboot=0, installerError=1, hr=0x800736b3
2016-10-19	14:24:02:668	8904	1834	Handler	:::::::::
2016-10-19	14:24:02:668	8904	1834	Handler	::  END  ::  Handler: CBS Install
</pre>

0x800736B3 - ERROR\_SXS\_ASSEMBLY\_NOT\_FOUND

Looking at the CBS.LOG showed me the following:

<pre class="lang:default decode:true">2016-10-19 14:24:01, Info                  CBS    Setting ExecuteState key to: CbsExecuteStateInitiateRollback | CbsExecuteStateFlagAdvancedInstallersFailed
2016-10-19 14:24:01, Info                  CSI    0000008a Performing 2 operations; 2 are not lock/unlock and follow:
  Install (5): flags: 0 tlc: [83df49984213f0fecd0a31e01bc60f57, Version = 6.1.7601.23505, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:154{77}]"Package_2_for_KB3177467~31bf3856ad364e35~amd64~~6.1.1.1.3177467-3_neutral_LDR" ncdata: [l:4{2}]"16") thumbprint: (null)
  Install (5): flags: 0 tlc: [466e94be62b208e51d86c55eba4b950e, Version = 6.1.7601.23505, pA = PROCESSOR_ARCHITECTURE_AMD64 (9), Culture neutral, VersionScope = 1 nonSxS, PublicKeyToken = {l:8 b:31bf3856ad364e35}, Type neutral, TypeName neutral, PublicKey neutral]) ref: ( flgs: 00000000 guid: {d16d444c-56d8-11d5-882d-0080c847b195} name: [l:154{77}]"Package_2_for_KB3177467~31bf3856ad364e35~amd64~~6.1.1.1.3177467-4_neutral_LDR" ncdata: [l:4{2}]"16") thumbprint: (null)
2016-10-19 14:24:01, Error                 CSI    0000008b@2016/10/19:20:24:01.356 (F) d:\win7sp1_gdr\base\wcp\componentstore\storelayout.cpp(8701): Error STATUS_SXS_ASSEMBLY_NOT_FOUND originated in function ComponentStore::CRawStoreLayout::QueryComponentUnstagedFiles expression: (null)
[gle=0x80004005]
2016-10-19 14:24:01, Error                 CSI    0000008c (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4475159# from CCSDirectTransaction::PerformChangeAnalysis(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    0000008d (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4475158# from CCSDirectTransaction::PrepareForCommit(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    0000008e (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4475157# from CCSDirectTransaction::ExamineTransaction(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    0000008f (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4475156# from CCSDirectTransaction_IRtlTransaction::ExamineTransaction(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    00000090@2016/10/19:20:24:01.481 (F) d:\win7sp1_gdr\base\wcp\componentstore\storelayout.cpp(8701): Error STATUS_SXS_ASSEMBLY_NOT_FOUND originated in function ComponentStore::CRawStoreLayout::QueryComponentUnstagedFiles expression: (null)
[gle=0x80004005]
2016-10-19 14:24:01, Error                 CSI    00000091 (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4477915# from CCSDirectTransaction::PerformChangeAnalysis(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    00000092 (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4477914# from CCSDirectTransaction::PrepareForCommit(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    00000093 (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4477913# from CCSDirectTransaction::GenerateComponentChangeList(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    00000094 (F) STATUS_SXS_ASSEMBLY_NOT_FOUND #4477912# from Windows::COM::CPendingTransaction::ExtractInformationFromRtlTransaction(...)[gle=0xd0150004]
2016-10-19 14:24:01, Error                 CSI    00000095 (F) HRESULT_FROM_WIN32(ERROR_SXS_ASSEMBLY_NOT_FOUND) #4474948# from Windows::COM::CPendingTransaction::IStorePendingTransaction_Analyze(...)[gle=0x800736b3]
2016-10-19 14:24:01, Error                 CSI    00000096 (F) HRESULT_FROM_WIN32(ERROR_SXS_ASSEMBLY_NOT_FOUND) #4474674# from Windows::ServicingAPI::CCSITransaction::ICSITransaction_Commit(Flags = 102 (0x00000066), pSink = NULL, disp = 0, coldpatching = FALSE)[gle=0x800736b3]
2016-10-19 14:24:01, Error                 CSI    00000097 (F) HRESULT_FROM_WIN32(ERROR_SXS_ASSEMBLY_NOT_FOUND) #4474673# 315357 us from Windows::ServicingAPI::CCSITransaction_ICSITransaction::Commit(flags = 0x00000066, pSink = NULL, disp = 0)
[gle=0x800736b3]
2016-10-19 14:24:01, Info                  CBS    Setting ExecuteState key to: ExecuteStateNone
2016-10-19 14:24:01, Info                  CBS    Setting RollbackFailed flag to 0
2016-10-19 14:24:01, Info                  CBS    Clearing HangDetect value
2016-10-19 14:24:01, Info                  CBS    Saved last global progress. Current: 0, Limit: 1, ExecuteState: ExecuteStateNone
2016-10-19 14:24:01, Error                 CBS    Exec: Failed to commit CSI transaction to execute changes. [HRESULT = 0x800736b3 - ERROR_SXS_ASSEMBLY_NOT_FOUND]
2016-10-19 14:24:01, Info                  CSI    00000098@2016/10/19:20:24:01.559 CSI Transaction @0x71be670 destroyed
2016-10-19 14:24:01, Info                  CBS    Perf: InstallUninstallChain complete.
2016-10-19 14:24:01, Info                  CBS    Failed to execute execution chain. [HRESULT = 0x800736b3 - ERROR_SXS_ASSEMBLY_NOT_FOUND]
2016-10-19 14:24:01, Error                 CBS    Failed to process single phase execution. [HRESULT = 0x800736b3 - ERROR_SXS_ASSEMBLY_NOT_FOUND]
2016-10-19 14:24:01, Info                  CBS    WER: Generating failure report for package: Package_for_KB3177467~31bf3856ad364e35~amd64~~6.1.1.1, status: 0x800736b3, failure source: Execute, start state: Staged, target state: Installed, client id: WindowsUpdateAgent</pre>

Ok, so now we can see the error but it still doesn't give us much information.  If I run ProcMon I can find \*when\* the error occurs somewhat easily because Windows Error Reporting kicks in as soon as the error occurs:

<img class="aligncenter size-large wp-image-1768" src="/wp-content/uploads/2016/10/windowsupdatebug4-1024x662.png" alt="windowsupdatebug4" width="1024" height="662" srcset="/wp-content/uploads/2016/10/windowsupdatebug4-1024x662.png 1024w, /wp-content/uploads/2016/10/windowsupdatebug4-300x194.png 300w, /wp-content/uploads/2016/10/windowsupdatebug4-768x497.png 768w, /wp-content/uploads/2016/10/windowsupdatebug4.png 1590w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

My apologies for such an ugly screenshot.  The 'Dark Blue' highlight is when Windows Error Reporting kicked in.  So the error must have occurred immediately preceding it.  The only line that had a 'NAME NOT FOUND' without being a subkey search is the line that ends in v!6.1.7601.18766.  If I browse to that location in the registry, here's what I find:

<img class="aligncenter size-full wp-image-1769" src="/wp-content/uploads/2016/10/windowsupdatebug5.png" alt="windowsupdatebug5" width="644" height="699" srcset="/wp-content/uploads/2016/10/windowsupdatebug5.png 644w, /wp-content/uploads/2016/10/windowsupdatebug5-276x300.png 276w" sizes="(max-width: 644px) 100vw, 644px" /> 

&nbsp;

So we are definitely missing that key.  I have a bit of an advantage with the patches here as I have a duplicate of this server in another vDisk that was made around a year ago.  Both are supposed to be at the same patch level, but it allows me to look through it's COMPONENTS registry hive and see if it has that key...

And I see a vastly different set of keys:

<img class="aligncenter size-full wp-image-1770" src="/wp-content/uploads/2016/10/windowsupdatebug6.png" alt="windowsupdatebug6" width="519" height="129" srcset="/wp-content/uploads/2016/10/windowsupdatebug6.png 519w, /wp-content/uploads/2016/10/windowsupdatebug6-300x75.png 300w" sizes="(max-width: 519px) 100vw, 519px" /> 

So I import that key and try running the update again...

<img class="aligncenter size-full wp-image-1771" src="/wp-content/uploads/2016/10/windowsupdatebug7.png" alt="windowsupdatebug7" width="550" height="386" srcset="/wp-content/uploads/2016/10/windowsupdatebug7.png 550w, /wp-content/uploads/2016/10/windowsupdatebug7-300x211.png 300w" sizes="(max-width: 550px) 100vw, 550px" /> 

&nbsp;

Manually, it successfully updated.  So now I tried running Windows Update again:

<img class="aligncenter size-full wp-image-1772" src="/wp-content/uploads/2016/10/windowsupdatebug8.png" alt="windowsupdatebug8" width="840" height="383" srcset="/wp-content/uploads/2016/10/windowsupdatebug8.png 840w, /wp-content/uploads/2016/10/windowsupdatebug8-300x137.png 300w, /wp-content/uploads/2016/10/windowsupdatebug8-768x350.png 768w" sizes="(max-width: 840px) 100vw, 840px" /> 

Code 8024400E.  Which means...  [I just need to rerun 'Try again' about 6 times](https://theorypc.ca/2016/03/14/wsus-clients-fail-with-warning-exceeded-max-server-round-trips-0x80244010/).  Once I do:

<img class="aligncenter size-full wp-image-1773" src="/wp-content/uploads/2016/10/windowsupdatebug9.png" alt="windowsupdatebug9" width="829" height="353" srcset="/wp-content/uploads/2016/10/windowsupdatebug9.png 829w, /wp-content/uploads/2016/10/windowsupdatebug9-300x128.png 300w, /wp-content/uploads/2016/10/windowsupdatebug9-768x327.png 768w, /wp-content/uploads/2016/10/windowsupdatebug9-750x320.png 750w" sizes="(max-width: 829px) 100vw, 829px" /> 

Ok, we have updates.  As a test I'm going to do just one:

<img class="aligncenter size-full wp-image-1774" src="/wp-content/uploads/2016/10/windowsupdatebug10.png" alt="windowsupdatebug10" width="728" height="274" srcset="/wp-content/uploads/2016/10/windowsupdatebug10.png 728w, /wp-content/uploads/2016/10/windowsupdatebug10-300x113.png 300w" sizes="(max-width: 728px) 100vw, 728px" /> 

&nbsp;

Success!

<img class="aligncenter size-full wp-image-1775" src="/wp-content/uploads/2016/10/windowsupdatebug11.png" alt="windowsupdatebug11" width="815" height="352" srcset="/wp-content/uploads/2016/10/windowsupdatebug11.png 815w, /wp-content/uploads/2016/10/windowsupdatebug11-300x130.png 300w, /wp-content/uploads/2016/10/windowsupdatebug11-768x332.png 768w" sizes="(max-width: 815px) 100vw, 815px" /> 

So I'll try the rest:

<img class="aligncenter size-full wp-image-1776" src="/wp-content/uploads/2016/10/windowsupdatebug12.png" alt="windowsupdatebug12" width="838" height="394" srcset="/wp-content/uploads/2016/10/windowsupdatebug12.png 838w, /wp-content/uploads/2016/10/windowsupdatebug12-300x141.png 300w, /wp-content/uploads/2016/10/windowsupdatebug12-768x361.png 768w" sizes="(max-width: 838px) 100vw, 838px" /> 

&nbsp;

Success!

<img class="aligncenter size-full wp-image-1777" src="/wp-content/uploads/2016/10/windowsupdatebug13.png" alt="windowsupdatebug13" width="823" height="363" srcset="/wp-content/uploads/2016/10/windowsupdatebug13.png 823w, /wp-content/uploads/2016/10/windowsupdatebug13-300x132.png 300w, /wp-content/uploads/2016/10/windowsupdatebug13-768x339.png 768w" sizes="(max-width: 823px) 100vw, 823px" /> 

&nbsp;

It appears we can install patches again.  I'm concerned the COMPONENTS hives were so different but without a solid understanding of how that hive comes to be (I wish you could rebuild it) I think I may be stuck with assuming that a failed update or maybe a corrupt registry key is at fault for that missing key breaking updates.  I guess we'll see what happens next month to see if we can patch Windows :/

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->