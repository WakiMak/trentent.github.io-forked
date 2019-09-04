---
id: 1508
title: 'Citrix Netscaler - Load balancing options with XML brokers'
date: 2016-06-17T12:48:39-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1508
permalink: /2016/06/17/citrix-netscaler-load-balancing-options-with-xml-brokers/
categories:
  - Blog
tags:
  - Citrix
  - Web Interface
---
We were encountering 'slowness' with our Citrix Web Interface that logging into it and displaying the applications would take a significant amount of time ( > 10 seconds). Â I have spent [considerable](http://theorypc.ca/2014/11/27/optimizing-citrix-web-interface-5-4/) [amount](http://theorypc.ca/2014/11/27/load-testing-citrix-xml-broker/) of time investigating what our performance should be in our environment. Â With this information we were still seeing these long logins. Â It was consistently atÂ 200 or greater concurrent connections on our web interface servers that we saw our login timesÂ spike. Â I know at ~200 or so connections our [response time is around 5,000ms](http://theorypc.ca/2014/11/27/load-testing-citrix-xml-broker/)Â for our XML brokers. Â IÂ then [used my PowerShell](http://theorypc.ca/2014/11/27/load-testing-citrix-xml-broker/) script to measure the response times of our XML broker's we are using in load balancing. Â We had two servers WI01 and WI02 that I monitored and as our web interface servers were becoming loaded I noticed something odd.

Only one of our XML brokers was being hit, the other was sitting idle. Â Since we use a VIP that is supposed to have load balancing I brought our network team who support the Netscalers to help me takeÂ a look.  
I configured two loads:  
1) Direct connections to the XML broker/VIP. This request simulates what the web interface sends to the XML broker on behalf of the user. The traffic POSTâ€™ed is an XML request for a list of all applications available to the user. This is the hardest request the web interface can send an XML broker because the XML broker needs to filter out applications the user does not have access to before it can send a response.  
2) Through the web interface. This request simulates going through the HTML pages to get your list of applications available on the web AND a PNA query for all applications available to the user.

To establish a baseline I â€˜loadedâ€™ a XML server directly (WSCTXAPP301T)

<img class="aligncenter size-full wp-image-1509" src="http://theorypc.ca/wp-content/uploads/2016/06/image003.png" alt="image003" width="625" height="520" srcset="http://theorypc.ca/wp-content/uploads/2016/06/image003.png 625w, http://theorypc.ca/wp-content/uploads/2016/06/image003-300x250.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> 

The burgundy line is 200 connections to the server posting a XML request for the list of all applications. When loading a single server the response time goes up to 5000ms.

I then loaded the VIP with 200 connections with the XML request.

<img class="aligncenter size-full wp-image-1510" src="http://theorypc.ca/wp-content/uploads/2016/06/image004.png" alt="image004" width="625" height="381" srcset="http://theorypc.ca/wp-content/uploads/2016/06/image004.png 625w, http://theorypc.ca/wp-content/uploads/2016/06/image004-300x183.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> 

From this we can see the â€˜loadâ€™ switch servers between the WSCTXZDC301T and WSCTXAPP301T. This is NOT load balancing, but the Netscaler failing over when \*it\* does not receive a reply from the XML monitor within 5000ms. The performance with PERSISTENCE set to SOURCEIP is no better than a single server for servicing requests.

Loading the Webinterface with requests:

<img class="aligncenter size-full wp-image-1511" src="http://theorypc.ca/wp-content/uploads/2016/06/image005.png" alt="image005" width="625" height="450" srcset="http://theorypc.ca/wp-content/uploads/2016/06/image005.png 625w, http://theorypc.ca/wp-content/uploads/2016/06/image005-300x216.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> 

We see the exact same situation when going through the web interface.

PERSISTENCE: NONE

After setting the PERSISTENCE to NONE I directed traffic directly to the VIP:

<img class="aligncenter size-full wp-image-1512" src="http://theorypc.ca/wp-content/uploads/2016/06/image006.png" alt="image006" width="625" height="453" srcset="http://theorypc.ca/wp-content/uploads/2016/06/image006.png 625w, http://theorypc.ca/wp-content/uploads/2016/06/image006-300x217.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> 

What we are seeing is for the exact same load (200 connections) our response time is 50% better with PERSISTENCE set to NONE. We are getting responses back in ~2300-3000ms.

Load testing the Web Interface with PERSISTENCE set to NONE sees the same results:

<img class="aligncenter size-full wp-image-1513" src="http://theorypc.ca/wp-content/uploads/2016/06/image007.png" alt="image007" width="625" height="488" srcset="http://theorypc.ca/wp-content/uploads/2016/06/image007.png 625w, http://theorypc.ca/wp-content/uploads/2016/06/image007-300x234.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> 

Whatâ€™s the impact on the end user by setting PERSISTENCE to NONE? There is no impact. During load testing I made several â€˜realâ€™ connections using Citrix Receiver via PNA and through the Web Interface and both continued to operate without issue enumerating applications. With this testing and information we need to change our PERSISTENCE value to NONE.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->