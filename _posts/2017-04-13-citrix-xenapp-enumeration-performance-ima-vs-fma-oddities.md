---
id: 2129
title: Citrix & Enumeration Performance - IMA vs FMA - oddities
date: 2017-04-13T12:20:32-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2129
permalink: /2017/04/13/citrix-&-enumeration-performance-ima-vs-fma-oddities/
image: /wp-content/uploads/2017/04/Screen-Shot-2017-04-13-at-12.19.57-PM.png
categories:
  - Blog
tags:
  - Citrix
  - Performance

---
During the course of [load testing FMA to see how it compares with IMA](https://theorypc.ca/2017/04/12/citrix-&-enumeration-performance-ima-vs-fma-load-testing/) in terms of performance I encountered some oddities with FMA.  It definitely is more efficient at enumerating & applications compared to IMA.  But...  The differences in my testing may have been overstated.

During the load testing I captured some performance counters.  One of them, "Citrix XML Service - enumerate resources - Concurrent Transactions" seemed to line up almost perfectly with the load wcat was applying to the broker.  BUT...  It capped out.  It seemed to cap at 200-220 concurrent connections.

<img class="aligncenter size-full wp-image-2130" src="/wp-content/uploads/2017/04/FMA_Oddity.png" alt="" width="1353" height="884" srcset="/wp-content/uploads/2017/04/FMA_Oddity.png 1353w, /wp-content/uploads/2017/04/FMA_Oddity-300x196.png 300w, /wp-content/uploads/2017/04/FMA_Oddity-768x502.png 768w" sizes="(max-width: 1353px) 100vw, 1353px" /> 

&nbsp;

The slope of the increase in the concurrent transactions should have continued but it stops.  I had set a maximum of 1000 concurrent connections via WCAT but it stops then does this weird 'stepping'.  When this limit appears to be reached, that appears to be when the "performance" of FMA succeeds IMA significantly.

<img class="aligncenter size-full wp-image-2131" src="/wp-content/uploads/2017/04/IMA_FMA_220.png" alt="" width="641" height="519" srcset="/wp-content/uploads/2017/04/IMA_FMA_220.png 641w, /wp-content/uploads/2017/04/IMA_FMA_220-300x243.png 300w" sizes="(max-width: 641px) 100vw, 641px" /> 

(ORANGE is FMA, BLUE is IMA).

What I'm considering is that FMA is taking all requests up to that limit and then 'queuing' them.  I have no proof of that outside of the Perf Counter just hitting that ceiling.  And when the concurrent connections hit ~800 and the response time soared, was the broker actually working?

No.  It was not working.  The responses I got when the connections hit 800+ looked like this:

<pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd"[]>
<NFuseProtocol version="5.9">
  <ResponseAppData>
  <ErrorId>unspecified</ErrorId>
  <LeasingStatus>working</LeasingStatus>
  </ResponseAppData>
</NFuseProtocol></pre>

ErrorID 'unspecified'.  Undoubtedly, it appears there is a timer somewhere that if the broker cannot respond to requests within a reasonable amount of time (20 seconds?) that it returns this error code.

So is the amount of processing the broker can handle a hardcoded limit?  I scanned the startup of the service with Procmon to see what registry keys/values the BrokerService was looking for.

<pre class="lang:reg decode:true">HKLM\SOFTWARE\Citrix\DesktopServer\BrokerStartupRetryPeriodLimitMs
HKLM\SOFTWARE\Citrix\DesktopServer\BrokerStartupRetryPeriodStartMaxMs
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\MaxItemsPerSyncCycle
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\PendingFailureMaxSecs
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\SyncCleanupDelaySecs
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\SyncIntervalSecs
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\SyncLocation
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\SyncStartDelayMins
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\UploadQueueIdleMaxSecs
HKLM\SOFTWARE\Citrix\DesktopServer\ConnectionLeasing\UploadQueueMaxItems
HKLM\SOFTWARE\Citrix\DesktopServer\ControllerStartupRetryPeriodLimitMs
HKLM\SOFTWARE\Citrix\DesktopServer\ControllerStartupRetryPeriodStartMaxMs
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\ConnectivityRetryDelaySecs
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\MaxConnectivityLossSecs
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\MaxTxRetries
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\MaxTxRetryIntervalMs
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\MinTxRetryIntervalMs
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\ProviderName
HKLM\SOFTWARE\Citrix\DesktopServer\DataStore\Connections\Controller\ReaperDeferralPeriodSecs
HKLM\SOFTWARE\Citrix\DesktopServer\DisablePerformanceCounters
HKLM\SOFTWARE\Citrix\DesktopServer\HostingStartupRetryPeriodLimitMs
HKLM\SOFTWARE\Citrix\DesktopServer\HostingStartupRetryPeriodStartMaxMs
HKLM\SOFTWARE\Citrix\DesktopServer\LHC\BackgroundTaskIntervalMS
HKLM\SOFTWARE\Citrix\DesktopServer\MaxPendingSessions
HKLM\SOFTWARE\Citrix\DesktopServer\MaxWorkers
HKLM\SOFTWARE\Citrix\DesktopServer\MinimumAcceptableVdaMajorVersion
HKLM\SOFTWARE\Citrix\DesktopServer\MinThreadPoolThreads
HKLM\SOFTWARE\Citrix\DesktopServer\WaitForDebuggerAttachedTimeoutSeconds
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\ListOfProxies
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\ProxyPortNumber
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_ArraySegmentPool_SegmentByteSize
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_ArraySegmentPool_SegmentsCount
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_CompressionLevel
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_Enabled
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_MaxBatchSize
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_MaxDispatchConcurrency
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_MaxDispatchThreadIdleTime
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_MaxPendingAcks
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_SendMultipleAcks
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_SendTimeout
HKLM\SOFTWARE\Citrix\DesktopServer\WorkerProxy\WebSocket_UseJsonEncoding
HKLM\SOFTWARE\Citrix\DesktopServer\XmlListeners
HKLM\SOFTWARE\Citrix\DesktopServer\XmlServicesEnableNonSsl
HKLM\SOFTWARE\Citrix\DesktopServer\XmlServicesEnableSsl
HKLM\SOFTWARE\Citrix\DesktopServer\XmlServicesTargetAddress
HKLM\SOFTWARE\Citrix\DesktopServer\XmsStartupRetryPeriodLimitMs
HKLM\SOFTWARE\Citrix\DesktopServer\XmsStartupRetryPeriodStartMaxMs
</pre>

I found a [document from Citrix that details \*some\* of these registry keys](http://support.citrix.com/article/CTX138738).

There is a registry value that determines the maximum number of concurrent requests the FMA broker will process:

<img class="aligncenter size-full wp-image-2132" src="/wp-content/uploads/2017/04/XMLListener.png" alt="" width="762" height="417" srcset="/wp-content/uploads/2017/04/XMLListener.png 762w, /wp-content/uploads/2017/04/XMLListener-300x164.png 300w" sizes="(max-width: 762px) 100vw, 762px" /> 

<img class="aligncenter size-full wp-image-2133" src="/wp-content/uploads/2017/04/XMLListenerRegKey.png" alt="" width="737" height="234" srcset="/wp-content/uploads/2017/04/XMLListenerRegKey.png 737w, /wp-content/uploads/2017/04/XMLListenerRegKey-300x95.png 300w" sizes="(max-width: 737px) 100vw, 737px" /> 

I added that key and restarted the service.  What was the impact?

16vCPU

<img class="aligncenter size-full wp-image-2137" src="/wp-content/uploads/2017/04/FMA_1380_XMLListener.png" alt="" width="1104" height="723" srcset="/wp-content/uploads/2017/04/FMA_1380_XMLListener.png 1104w, /wp-content/uploads/2017/04/FMA_1380_XMLListener-300x196.png 300w, /wp-content/uploads/2017/04/FMA_1380_XMLListener-768x503.png 768w" sizes="(max-width: 1104px) 100vw, 1104px" /> 

Very positive.  The 800 concurrent limit was easily bypassed. Why is this limited to 500 by default?  Changing this value to 1000 had a massive improvement in terms of how many concurrent connections it could handle.  It pushed itself to 1350 concurrent connections before it broke.  If I had to push for a single tweak to FMA, this one might be it.  Mind you, the Performance Counter was still capping itself around 220 concurrent connections, so I'm not sure if that's a limit of the counter or something else but it is VERY accurate until that point.  If I start my WCAT test and go to 50 the perf counter goes to 50.  If I choose 80, it goes to 80.  If I choose 173 it goes to exactly 173.  So it is odd.

During the course of my investigation I found a registry key that shows Citrix has decided any request over 20 seconds to the broker will fail.  This key is:

<img class="aligncenter size-full wp-image-2140" src="/wp-content/uploads/2017/04/xmlwpnbrrequesttimeoutms.png" alt="" width="758" height="91" srcset="/wp-content/uploads/2017/04/xmlwpnbrrequesttimeoutms.png 758w, /wp-content/uploads/2017/04/xmlwpnbrrequesttimeoutms-300x36.png 300w, /wp-content/uploads/2017/04/xmlwpnbrrequesttimeoutms-750x91.png 750w" sizes="(max-width: 758px) 100vw, 758px" /> 

I suspect this is my "unspecified" error.  20 seconds is a really long time for a broker to respond to a request though, if you were a user this would suck.  However, is it better to increase this value?  I have it on my todo list to try and increase it for my testing to see what happens, but values like the Storefront or Netscaler XML requests to see if the brokers are responding are handicapped to 20 seconds with this value.  So it's probably important to have them match to reduce any further timeout you may encounter.  For instance, it would not make sense to set a 60 second timeout in a Netscaler XML broker test if the broker is going to timeout at 20 seconds anyways.

Next will be testing this with Local Host Cache to see if this causes any impact.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->