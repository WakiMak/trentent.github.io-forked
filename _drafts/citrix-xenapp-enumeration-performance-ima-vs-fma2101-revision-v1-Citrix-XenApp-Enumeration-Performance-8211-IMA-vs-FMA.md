---
id: 2139
title: 'Citrix XenApp Enumeration Performance &#8211; IMA vs FMA'
date: 2017-04-12T15:37:36-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/04/12/2101-revision-v1/
permalink: /2017/04/12/2101-revision-v1/
---
We&#8217;re exploring upgrading our Citrix XenApp 6.5 environment to 7.X (currently 7.13) and we have some architecture decisions that are driven by the performance of the infrastructure components of XenApp. Â In 6.5 these components are theÂ &#8220;Citrix Independent Management Architecture&#8221; and in 7.13 this is the &#8220;Citrix Broker Service&#8221;. Â The performance I&#8217;ll be measuring is how long it takes to enumerate applications for a user. Â In XenApp 6.5 this is the most intensive task put on the broker. Â I&#8217;ve taken our existing XenApp 6.5 TEST environment and &#8220;migrated&#8221; it to 7.X. Â The details of the environment are 189 enabled applications with various security groups applied to each application. Â The user I will be testing with has access to 55Â of them. Â What the broker/IMA service has to do when it receives the XML request is evaluate each application to see if the user has permissions and return the results. Â The &#8216;request&#8217; is slightly different to the broker vs the IMA. Â This is what the FMA requests will look like:

<pre class="lang:ps decode:true ">$soap7XD = @'
&lt;NFuseProtocol version="5.5"&gt;
    &lt;RequestAppData&gt;
        &lt;Scope Traverse="subtree"&gt;&lt;/Scope&gt;
        &lt;DesiredDetails&gt;permissions&lt;/DesiredDetails&gt;
        &lt;ServerType&gt;all&lt;/ServerType&gt;
        &lt;ClientType&gt;ica30&lt;/ClientType&gt;
        &lt;ClientType&gt;rade&lt;/ClientType&gt;
        &lt;ClientType&gt;content&lt;/ClientType&gt;
        &lt;Credentials&gt;
            &lt;UserName&gt;adtest90&lt;/UserName&gt;
            &lt;Password encoding="ctx1"&gt;PASSWORDLOL&lt;/Password&gt;
            &lt;Domain type="NT"&gt;HEALTHY&lt;/Domain&gt;
        &lt;/Credentials&gt;
        &lt;Clientname&gt;LOADTESTER&lt;/Clientname&gt;
        &lt;ClientAddress addresstype="dot"&gt;10.10.10.10&lt;/ClientAddress&gt;
    &lt;/RequestAppData&gt;
&lt;/NFuseProtocol&gt;
'@</pre>

&nbsp;

And the IMA requests:

<pre class="lang:ps decode:true ">$soap = @'
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd"&gt;
&lt;NFuseProtocol version="5.4"&gt;
    &lt;RequestAppData&gt;
        &lt;Scope traverse="subtree"&gt;&lt;/Scope&gt;
        &lt;DesiredDetails&gt;permissions&lt;/DesiredDetails&gt;
        &lt;ServerType&gt;all&lt;/ServerType&gt;
        &lt;ClientType&gt;ica30&lt;/ClientType&gt;
        &lt;ClientType&gt;content&lt;/ClientType&gt;
        &lt;Credentials&gt;
            &lt;UserName&gt;adtest90&lt;/UserName&gt;
            &lt;Password encoding="ctx1"&gt;PASSWORDLOL&lt;/Password&gt;
            &lt;Domain type="NT"&gt;HEALTHY&lt;/Domain&gt;
        &lt;/Credentials&gt;
        &lt;ClientName&gt;LOADTESTER&lt;/ClientName&gt;
        &lt;ClientAddress addresstype="dot"&gt;10.10.10.10&lt;/ClientAddress&gt;
    &lt;/RequestAppData&gt;
&lt;/NFuseProtocol&gt;
'@</pre>

In our environment, we have measured a &#8216;peak&#8217; load of 600 concurrent connections per second to our XenApp 6.5 IMA service. Â We split this load over 7 servers and the load is load-balanced via Netscaler VIP&#8217;s. Â This lessens the peak load toÂ 85 concurrent connections per server per second. Â What&#8217;s a &#8220;connection&#8221;? Â A connection is a request to theÂ IMA service and a response from it. Â This would be considered a single connection in my definition:

<img class="aligncenter size-full wp-image-2102" src="http://theorypc.ca/wp-content/uploads/2017/04/Connection.png" alt="" width="542" height="595" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Connection.png 542w, http://theorypc.ca/wp-content/uploads/2017/04/Connection-273x300.png 273w" sizes="(max-width: 542px) 100vw, 542px" /> 

&nbsp;

This is a single request (in RED) and response (in BLUE). Â No further follow up is required by the client.

I&#8217;m going to profile a single response and request to better understand the individual performance of each product.

This is what my network traffic will look like (on the 7.X broker service):

<pre class="lang:xhtml decode:true">POST /scripts/wpnbr.dll HTTP/1.1
Content-Type: text/xml
Host: wsctxddc2001t
Content-Length: 676
Expect: 100-continue
Connection: Keep-Alive

HTTP/1.1 100 Continue

&lt;NFuseProtocol version="5.5"&gt;
    &lt;RequestAppData&gt;
        &lt;Scope Traverse="subtree"&gt;&lt;/Scope&gt;
        &lt;DesiredDetails&gt;permissions&lt;/DesiredDetails&gt;
        &lt;ServerType&gt;all&lt;/ServerType&gt;
        &lt;ClientType&gt;ica30&lt;/ClientType&gt;
        &lt;ClientType&gt;rade&lt;/ClientType&gt;
        &lt;ClientType&gt;content&lt;/ClientType&gt;
        &lt;Credentials&gt;
            &lt;UserName&gt;adtest90&lt;/UserName&gt;
            &lt;Password encoding="ctx1"&gt;PASSWORDLOL&lt;/Password&gt;
            &lt;Domain type="NT"&gt;HEALTHY&lt;/Domain&gt;
        &lt;/Credentials&gt;
        &lt;Clientname&gt;LOADTESTER&lt;/Clientname&gt;
        &lt;ClientAddress addresstype="dot"&gt;10.10.10.10&lt;/ClientAddress&gt;
    &lt;/RequestAppData&gt;
&lt;/NFuseProtocol&gt;HTTP/1.1 200 OK
Content-Length: 23712
Content-Type: text/xml
Server: Microsoft-HTTPAPI/2.0
Date: Tue, 11 Apr 2017 15:38:37 GMT

&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd"&gt;
&lt;NFuseProtocol version="5.9"&gt;
    &lt;ResponseAppData&gt;
        &lt;LeasingStatus&gt;working&lt;/LeasingStatus&gt;
        &lt;AppDataSet&gt;
            &lt;Scope&gt;&lt;/Scope&gt;
            &lt;AppData&gt;
                &lt;InName&gt;Notepad&lt;/InName&gt;
                &lt;FName&gt;Notepad 2016&lt;/FName&gt;
                &lt;Details&gt;
                &lt;/Details&gt;
                &lt;SeqNo&gt;-1770193386&lt;/SeqNo&gt;
                &lt;ServerType&gt;win32&lt;/ServerType&gt;
                &lt;ClientType&gt;ica30&lt;/ClientType&gt;
                &lt;Permissions&gt;
                &lt;/Permissions&gt;
            &lt;/AppData&gt;
            &lt;AppData&gt;
                &lt;InName&gt;Notepad 2016 - PLB&lt;/InName&gt;
                &lt;FName&gt;Notepad 2016 - PLB&lt;/FName&gt;
                &lt;Details&gt;
                &lt;/Details&gt;
                &lt;SeqNo&gt;-2064164981&lt;/SeqNo&gt;
                &lt;ServerType&gt;win32&lt;/ServerType&gt;
                &lt;ClientType&gt;ica30&lt;/ClientType&gt;
                &lt;Permissions&gt;
                &lt;/Permissions&gt;
            &lt;/AppData&gt;
... 70 applications later...
            &lt;AppData&gt;
                &lt;InName&gt;Test notepad-wsct-2&lt;/InName&gt;
                &lt;FName&gt;Test notepad-wsctxapp401t&lt;/FName&gt;
                &lt;Details&gt;
                &lt;/Details&gt;
                &lt;SeqNo&gt;-1347715524&lt;/SeqNo&gt;
                &lt;ServerType&gt;win32&lt;/ServerType&gt;
                &lt;ClientType&gt;ica30&lt;/ClientType&gt;
                &lt;Permissions&gt;
                &lt;/Permissions&gt;
            &lt;/AppData&gt;
        &lt;/AppDataSet&gt;
    &lt;/ResponseAppData&gt;
&lt;/NFuseProtocol&gt;
</pre>

The total time between when the FMA broker receives a single request to beginning the response is:

<img class="aligncenter size-full wp-image-2103" src="http://theorypc.ca/wp-content/uploads/2017/04/Receipt_to_start_of_response.png" alt="" width="946" height="66" srcset="http://theorypc.ca/wp-content/uploads/2017/04/Receipt_to_start_of_response.png 946w, http://theorypc.ca/wp-content/uploads/2017/04/Receipt_to_start_of_response-300x21.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/Receipt_to_start_of_response-768x54.png 768w" sizes="(max-width: 946px) 100vw, 946px" /> 

Initial receipt of traffic at 37.567664-37.567827.  
Response starts at 37.633986

<img class="aligncenter size-full wp-image-2104" src="http://theorypc.ca/wp-content/uploads/2017/04/end_of_response.png" alt="" width="954" height="51" srcset="http://theorypc.ca/wp-content/uploads/2017/04/end_of_response.png 954w, http://theorypc.ca/wp-content/uploads/2017/04/end_of_response-300x16.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/end_of_response-768x41.png 768w" sizes="(max-width: 954px) 100vw, 954px" /> 

Response ends at 37.634432.

Total time for FMA request for list of applications and the response for that list:

<img class="aligncenter size-full wp-image-2105" src="http://theorypc.ca/wp-content/uploads/2017/04/FMA_details.png" alt="" width="256" height="160" /> 

For IMA the total time between when the IMA service receives a single request to beginning response is:

<img class="aligncenter size-full wp-image-2111" src="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Request_from_client-3.png" alt="" width="954" height="81" srcset="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Request_from_client-3.png 954w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Request_from_client-3-300x25.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Request_from_client-3-768x65.png 768w" sizes="(max-width: 954px) 100vw, 954px" /> 

Initial receipt of traffic at 38.359944-38.360198.  
Response starts at 38.440197

<img class="aligncenter size-full wp-image-2112" src="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Receipt_to_start_of_response.png" alt="" width="1025" height="62" srcset="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Receipt_to_start_of_response.png 1025w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Receipt_to_start_of_response-300x18.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Receipt_to_start_of_response-768x46.png 768w" sizes="(max-width: 1025px) 100vw, 1025px" /> 

<img class="aligncenter size-full wp-image-2113" src="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Response_End-1.png" alt="" width="984" height="65" srcset="http://theorypc.ca/wp-content/uploads/2017/04/IMA_Response_End-1.png 984w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Response_End-1-300x20.png 300w, http://theorypc.ca/wp-content/uploads/2017/04/IMA_Response_End-1-768x51.png 768w" sizes="(max-width: 984px) 100vw, 984px" /> 

Response ends at 38.450032.

Total time for IMA request for list of applications and the response for that list:

<img class="aligncenter size-full wp-image-2114" src="http://theorypc.ca/wp-content/uploads/2017/04/IMA_traffic_stats-1.png" alt="" width="255" height="161" /> 

Why the size difference (18KB vs 24KB)?

Looking at the data returned from the FMA via the IMA shows there is a new field passed by the FMA broker as apart of &#8216;AppData&#8217;

<pre class="lang:default decode:true ">&lt;Permissions&gt;
                &lt;/Permissions&gt;</pre>

These two lines add 61 bytes per application. Â A standard application response is (with title) ~331 bytes per IMA application and ~400 bytes per FMA application.

However, these single request are exactly that. Â Single.

In order to get a better feel I ran the requests continuously in a loop, sending a request the FMA than the IMA, delay 1 second, and resend. Â This should get me a more accurate feel for the performance differences. Â I ran this over a period of 10 minutes. Â My results were:

&nbsp;

<img class="aligncenter size-full wp-image-2115" src="http://theorypc.ca/wp-content/uploads/2017/04/FMA_vs_IMA.png" alt="" width="343" height="57" srcset="http://theorypc.ca/wp-content/uploads/2017/04/FMA_vs_IMA.png 343w, http://theorypc.ca/wp-content/uploads/2017/04/FMA_vs_IMA-300x50.png 300w" sizes="(max-width: 343px) 100vw, 343px" /> 

IMA is faster by approx 30ms per request.

[Next up is load testing IMA vs FMA](https://theorypc.ca/2017/04/12/citrix-xenapp-enumeration-performance-ima-vs-fma-load-testing/).

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->