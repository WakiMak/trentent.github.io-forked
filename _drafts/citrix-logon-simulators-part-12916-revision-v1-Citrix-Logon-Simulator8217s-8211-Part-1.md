---
id: 2957
title: 'Citrix Logon Simulator&#8217;s &#8211; Part 1'
date: 2019-02-06T12:55:33-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2019/02/06/2916-revision-v1/
permalink: /2019/02/06/2916-revision-v1/
---
&#8220;Help!Â I can&#8217;t launch my application!&#8221;  
&#8220;Is something wrong with Citrix?&#8221;  
&#8220;Hey man, I heard Citrix was down?&#8221;  
&#8220;Can you help? I need to get this work done!Â The deadline is today and I can&#8217;t open my app!&#8221;

<img class="aligncenter wp-image-2918 size-medium" src="http://theorypc.ca/wp-content/uploads/2019/01/sad-300x264.png" alt="" width="300" height="264" srcset="http://theorypc.ca/wp-content/uploads/2019/01/sad-300x264.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/sad.png 750w" sizes="(max-width: 300px) 100vw, 300px" /> 

Welcome to the world of a Citrix Administrator.Â If an application stops working then the calls flood in and you get pinged a million times.Â Is there a way to be proactive about when an application goes down so you maximize your time trying to fix the issue between the failure and first call?

The answer to this is a Citrix Logon Simulator.

There are a few different logon simulators out there including two powershell scripts I wrote for performance testing.Â One for testing the [&#8220;Web&#8221; service](https://theorypc.ca/2017/05/24/citrix-storefront-performance-testing-and-tuning-part-1/) and one for the [&#8220;Store&#8221; service](https://theorypc.ca/2017/05/30/citrix-storefront-performance-and-tuning-part-2-pna-traffic-simulation/).Â The difference between the two services is found in the name, with &#8220;Web&#8221; typically appended to the web service (eg, &#8220;/Citrix/StoreWeb&#8221;) and the &#8220;Store&#8221; service ending thusly (eg, &#8220;/Citrix/Store&#8221;).

The &#8220;Web&#8221; service is the user-facing front end for Storefront.Â When you open a browser and go to your Storefront URL you are using the web service.

<div id="attachment_2920" style="width: 700px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2920" class="wp-image-2920 size-full" src="http://theorypc.ca/wp-content/uploads/2019/01/LogonStorefRont.png" alt="" width="690" height="388" srcset="http://theorypc.ca/wp-content/uploads/2019/01/LogonStorefRont.png 690w, http://theorypc.ca/wp-content/uploads/2019/01/LogonStorefRont-300x169.png 300w" sizes="(max-width: 690px) 100vw, 690px" /></p> 
  
  <p id="caption-attachment-2920" class="wp-caption-text">
    &#8220;Web&#8221; service. User logs into Storefront and launches an app using a web browser
  </p>
</div>

Each &#8220;Web&#8221; service has a corresponding &#8220;Store&#8221; service.Â However, the &#8220;Store&#8221; serviceÂ **_does not require_** a &#8220;Web&#8221; service and you can create Store services without a Web Service.Â Store services are used when you configure Citrix Reciever/Workspace App to connect to Storefront.Â When you launch apps via Citrix Receiver/Workspace App you are using the Store service.Â In addition, when thin clients are configured to use Storefront they, typically, use the Store service.

<div id="attachment_2921" style="width: 1081px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2921" class="wp-image-2921 size-full" src="http://theorypc.ca/wp-content/uploads/2019/01/StoreService.png" alt="" width="1071" height="788" srcset="http://theorypc.ca/wp-content/uploads/2019/01/StoreService.png 1071w, http://theorypc.ca/wp-content/uploads/2019/01/StoreService-300x221.png 300w, http://theorypc.ca/wp-content/uploads/2019/01/StoreService-768x565.png 768w" sizes="(max-width: 1071px) 100vw, 1071px" /></p> 
  
  <p id="caption-attachment-2921" class="wp-caption-text">
    Using the &#8220;Store&#8221; service. Not the program is &#8220;Citrix Workspace&#8221; and not a web browser.
  </p>
</div>

I haven&#8217;t found a logon simulator that tests each service, the products out there only test one or the other.

## In Summary

#### Web Service

Testing the web service simulates a users experience authenticating and launching an application via a web browser.Â These simulators launch a web browser, browse to your URL, login, find your application and then click it to launch it.Â This does make an impressive demo of automation watching the web browser do actions without a human.

#### Store Service

Testing the store service simulates a user authenticating and launching an application, via a Citrix client.Â This is done through API&#8217;s or REST API calls, it&#8217;s up to the client to generate a GUI from the information returned (if desired).

## Drawbacks

### Web Service Simulators

Executing the logon simulators that use the web service is impressive watching the web browser manipulate itself.Â However, the requirement of using a web browser was limiting in that executing **multiple concurrent applications** was not feasible.Â Options seemed to range from providing a list of applications to be tested (which are tested sequentially) or expanding the number of VM&#8217;s that will run the logon simulator. Â In addition, it&#8217;s possible to create &#8220;Store&#8221; services that host applications without the corresponding &#8220;Web&#8221; service. Simulators that only test the Web Service will be unable to do any type of testing for these Stores.

#### Single VM

If you opt for a single VM to test as many applications as possible, the time between applications increases linearly.Â For 10 applications to be tested to validate they are operational, with a 2 minute interval you will only be testing that application every 20 minutes (at best).Â If you have 100 applications you&#8217;ll be waiting over 3 hours between tests.

#### Multiple VM&#8217;s

Multiple VM&#8217;s operate very well with the web service.Â But they introduce another wrinkle.Â How feasible is it to expand your environment 100 VM&#8217;s to test if 100 applications are operational?Â With a operating system overhead of ~1GB at best, you&#8217;ll be consuming at least 100vCPU and 100GB more of memory.Â This is pretty much dedicating a host just for robots.Â This may be worth it when compared to the cost of time when a application is down&#8230; But this is now becoming a very, very expensive solution.Â Hardware costs for hosts, hypervisor licenses, and Windows licenses compound to the pain of a multi-VM solution.

### Store ServiceÂ Simulators

In terms of a demo, the store service is lessÂ _visually_ impressive.Â The automation is done programmatically so you don&#8217;t get to see those gratifying &#8220;clicks&#8221;.Â However, there is no browser requirement.Â This does mean you don&#8217;t need a full GUI operating system to run a store service logon simulator.Â In addition, because the Store Service simulator operates via API calls, it&#8217;s completely possible to run multipleÂ _**inÂ**_ **_parallel_**.Â This means there is a very large opportunity to save VM costs by consolidating all the testing into one, or just a couple VM&#8217;s and for each test it will keep that tight interval. Â However, the draw back of the Store Service simulator is additional configuration is required for testing through a Netscaler. Â Essentially, if you have not setup [the DNS SRV record](https://www.citrix.com/blogs/2013/04/01/configuring-email-based-account-discovery-for-citrix-receiver/) to allow Store Service communication than a Store Service simulator will not work externally.

## What&#8217;s the plan?

After exploring these considerations, I set out to design something with some goals.

  1. Minimize the number of VM&#8217;s to run the robots
  2. As little resource consumption as possible
  3. Still provide operational alerts
  4. Operate on-premise

[My next post will explore how to achieve this and the solution I settled on](https://theorypc.ca/2019/02/06/citrix-logon-simulators-part-2/).

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->