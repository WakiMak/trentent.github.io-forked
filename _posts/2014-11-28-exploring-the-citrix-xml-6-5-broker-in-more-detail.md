---
id: 582
title: Exploring the Citrix XML 6.5 Broker in more detail
date: 2014-11-28T12:00:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/11/28/exploring-the-citrix-xml-6-5-broker-in-more-detail/
permalink: /2014/11/28/exploring-the-citrix-xml-6-5-broker-in-more-detail/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/11/exploring-citrix-xml-65-broker-in-more.html
blogger_internal:
  - /feeds/7977773/posts/default/1858331604719625347
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance
  - &
---
The Citrix XML broker actually relies on many pieces to ensure fast and proper operation. [This CTX article](http://support.citrix.com/article/CTX129585) describes the process for & 6 (seems applicable to 6.5 as well).

The part that is relevant to the XML broker is steps 4-9.

4. The userâ€™s credentials are forwarded from XML to the IMA service in HTTP (or HTTPS) form.

5. The IMA then forwards them to the local Lsass.exe.

6. The Lsass.exe encrypts the credentials and passes them to the domain controller.

7. The domain controller returns the SIDs (userâ€™s SID and the list of group SIDs) back to Lsass.exe and to IMA.

8. IMA uses the SIDs to search the Local Host Cache (LHC) for a list of applications and the Worker Group Preference policy for that authenticated user.

9. The list of the applications together with the userâ€™s worker group preference policy are returned to the Web Interface.

So what does this look like (click to blow it up)?

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-1rSKuzoc5Mg/VHiJSx9vh8I/AAAAAAAAAsA/ghrk1JK0h9o/s1600/Packet-Flow.png"><img src="http://2.bp.blogspot.com/-1rSKuzoc5Mg/VHiJSx9vh8I/AAAAAAAAAsA/ghrk1JK0h9o/s1600/Packet-Flow.png" width="500" height="640" border="0" /></a>
</div>

Starting with packet #32 we see the initial POST request for a list of applications.

<pre class="lang:default decode:true "><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
	<NFuseProtocol version="5.4">
		<RequestAppData>
			<Scope traverse="subtree"></Scope>
			<DesiredDetails>permissions</DesiredDetails>
			<ServerType>all</ServerType>
			<ClientType>ica30</ClientType>
			<ClientType>content</ClientType>
			<Credentials>
				<UserName>trententtye</UserName>
				<Password encoding="ctx1">PASSWORDLOL</Password>
				<Domain type="NT">HEALTHY</Domain>
			</Credentials>
			<ClientName>WSCTXAPP301T</ClientName>
			<ClientAddress addresstype="dot">10.132.169.130</ClientAddress>
		</RequestAppData>
	</NFuseProtocol></pre>

Steps 5, 6 and 7 are packets 36-86. Â LSASS goes back to AD to grab the SID's.

Step 7.5: It appears that the first time you enumerate applications on a broker that information is queried to the SQL database and stored in the local host cache. Â This would be packets 87-94. Â Additional queries done do not show this traffic.

<pre class="lang:default decode:true">R JoinIndexSearch2 & & & 4 0 & imalock 8nodeid & uidhost & uidtype & uidid & contextid rdn " KEYTABLE data 
 | 
 &7cb1-0192-00000a90 dummy textptr dummyTS 
 1 PolicyObjectClass 9 ATTRIBUTE-uid uidvalue 
 |3 ATTRIBUTE-imadn css * & 7cb1-0192-00000a90 A ATTRIBUTE-contextID uint32 E ATTRIBUTE-updateCount uint32 Name cis R N Application-Millennium-Failover-Policy + Description css R N Application Millennium Failover Policy ) Version uint32 + Priority uint32 ' 
Enabled uint8 - 
ExtEnabled uint8 1 
FilterByUser uint8 G 
AllowAuthenticatedUsers uint8 ? 
AllowAnonymousUsers uint8 9 
FilterByClientIp uint8 ; 
AllowAllClientIps uint8 = 
FilterByClientName uint8 ? 
AllowAllClientNames uint8 G 
FilterByAccessCondition uint8 ? 
AccessConditionFlag uint8 [ WorkerGroupPreferenceAndFailover uint32 A StreamedAppDelivery uint32 _ StreamedAppDelivery!DeliveryOption uint32 { WorkerGroupPreferenceAndFailover!WorkerGroupUidList css  * 1=7CB1-0350-00000A8C * 2=7CB1-0350-00000A8E C AccessSessionConditions css 1 DeniedNameList css 3 AllowedNameList css - DeniedIpList css / AllowedIpList css ) DeniedList css + AllowedList css 0X2/NT/HEALTHY/S-1-5-21-38857442-2693285798-3636612711-15112741 0X2/NT/HEALTHY/S-1-5-21-38857442-2693285798-3636612711-15112748 0X2/NT/HEALTHY/S-1-5-21-38857442-2693285798-3636612711-15112746 0X2/NT/HEALTHY/S-1-5-21-38857442-2693285798-3636612711-15112752 y</pre>

Lastly, step 9 is all the traffic we see after packet 95 in red; the return of the XML data. Â For our setup, our XML brokers responded with the following timings:

Step 4: 1ms  
Step 5: 2ms  
Steps 6 and 7: 47ms  
Step 7.5 (DB query is not in LHC): 2ms  
Step 8: 14ms  
Step 9: 14ms

Total: 80ms

With a freshly created LHC this is what process monitor sees:

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-ywncltYRguM/VHiTt0wMJAI/AAAAAAAAAsQ/HtEQktbm59I/s1600/Screen%2BShot%2B2014-11-28%2Bat%2B8.22.15%2BAM.png"><img src="http://1.bp.blogspot.com/-ywncltYRguM/VHiTt0wMJAI/AAAAAAAAAsQ/HtEQktbm59I/s1600/Screen%2BShot%2B2014-11-28%2Bat%2B8.22.15%2BAM.png" width="640" height="344" border="0" /></a>
</div>

Again, the IMASrv.exe will actually NOT be present in this list if you have executed at least one query against the XML broker as it is only displayed when it queries the database (wssql011n02) and stores the response in the LHC.

So, what could contribute to slow XML broker performance?

Utilizing the WCAT script we can continuously hammer the XML broker with however many connections we desire. Â The XML brokers have a maximum of 16 threads to deal with the incoming traffic but at 80ms per request/response the queue would have to get fairly long to create a noticeable performance impact. Â In addition, previous tests on the 4.5 broker showed additional CPU's help improve the performance of the XML broker, I think the 6.5 broker shows better performance with lesser CPU's.

Utilizing the bandwidth emulator, [clumsy](http://jagt.github.io/clumsy/), I'm going to simulate some poor network performance on the XML broker to see what the effects will be of how XML response times will vary. Â The only network hits I can see are from the source (Web Interface), Active Directory, and (potentially) the SQL database.

Just starting the clumsy software with it's filtering by IP capabilities added about 800ms to the total roundtrip time. Â Something to consider with other network management/threat protection software I imagine...

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-_Nx8LtE3IjE/VHirc_RlqzI/AAAAAAAAAsg/HacU96cPu10/s1600/Screen%2BShot%2B2014-11-28%2Bat%2B10.04.38%2BAM.png"><img src="http://4.bp.blogspot.com/-_Nx8LtE3IjE/VHirc_RlqzI/AAAAAAAAAsg/HacU96cPu10/s1600/Screen%2BShot%2B2014-11-28%2Bat%2B10.04.38%2BAM.png" width="320" height="196" border="0" /></a>
</div>

Anyways, adding just 20ms of lag to and from the web interface to the XML broker increased processing time by another 500ms of total time. Â That is, 1500ms on the low end on 2200 on the high. Â Increasing packet latency to 100ms brought the total processing time to 2700ms on the lowend and 3800ms on the high end. Â I think it's safe to say that having the web interface and XML brokers beside each other for the lowest possible latency is a big performance win.

Targeting Active Directory with a latency of 20ms brought times from 420ms to 550ms. Â Increasing that to 100ms brought the response times put to 890ms. Â Not too shabby. Â Seems AD is more resilient to latency.

Targeting the SQL database with a latency of 100ms showed the first query after the LHC was rebuilt went to 600ms and then back down to 420ms there after. Â Locality to the database seems to have the lowest impact, but the 100ms lag did increase the LHC rebuild time to about 3 minutes from near instantly before.  
I did try to test a heavy disk load against the XML broker but I was running this server with PVS with RAMDisk Cache overflow enabled which means my LHC is stored in RAM and no matter how hard I punted the C:\ drive I couldn't make an impact.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->