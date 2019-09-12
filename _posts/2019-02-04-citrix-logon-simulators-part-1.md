---
id: 2916
title: Citrix Logon Simulator's - Part 1
date: 2019-02-04T11:00:26-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2916
permalink: /2019/02/04/citrix-logon-simulators-part-1/
categories:
  - Blog
tags:
  - Citrix
  - Citrix Receiver
  - Logons
  - Performance
  - Storefront

  - XenDesktop
---
"Help! I can't launch my application!"  
"Is something wrong with Citrix?"  
"Hey man, I heard Citrix was down?"  
"Can you help? I need to get this work done! The deadline is today and I can't open my app!"

<img class="aligncenter wp-image-2918 size-medium" src="/wp-content/uploads/2019/01/sad-300x264.png" alt="" width="300" height="264" srcset="/wp-content/uploads/2019/01/sad-300x264.png 300w, /wp-content/uploads/2019/01/sad.png 750w" sizes="(max-width: 300px) 100vw, 300px" /> 

Welcome to the world of a Citrix Administrator. If an application stops working then the calls flood in and you get pinged a million times. Is there a way to be proactive about when an application goes down so you maximize your time trying to fix the issue between the failure and first call?

The answer to this is a Citrix Logon Simulator.

There are a few different logon simulators out there including two powershell scripts I wrote for performance testing. One for testing the ["Web" service](https://theorypc.ca/2017/05/24/citrix-storefront-performance-testing-and-tuning-part-1/) and one for the ["Store" service](https://theorypc.ca/2017/05/30/citrix-storefront-performance-and-tuning-part-2-pna-traffic-simulation/). The difference between the two services is found in the name, with "Web" typically appended to the web service (eg, "/Citrix/StoreWeb") and the "Store" service ending thusly (eg, "/Citrix/Store").

The "Web" service is the user-facing front end for Storefront. When you open a browser and go to your Storefront URL you are using the web service.

<figure class="wp-block-image"><img src="/wp-content/uploads/2019/01/LogonStorefRont.png"/><figcaption>"Web" service. User logs into Storefront and launches an app using a web browser</figcaption></figure> 

Each "Web" service has a corresponding "Store" service. However, the "Store" service **_does not require_** a "Web" service and you can create Store services without a Web Service. Store services are used when you configure Citrix Reciever/Workspace App to connect to Storefront. When you launch apps via Citrix Receiver/Workspace App you are using the Store service. In addition, when thin clients are configured to use Storefront they, typically, use the Store service.

<figure class="wp-block-image"><img src="/wp-content/uploads/2019/01/StoreService.png"/><figcaption>Using the "Store" service. Not the program is "Citrix Workspace" and not a web browser.</figcaption></figure> 

I haven't found a logon simulator that tests each service, the products out there only test one or the other.

## In Summary

#### Web Service

Testing the web service simulates a users experience authenticating and launching an application via a web browser. These simulators launch a web browser, browse to your URL, login, find your application and then click it to launch it. This does make an impressive demo of automation watching the web browser do actions without a human.

#### Store Service

Testing the store service simulates a user authenticating and launching an application, via a Citrix client. This is done through API's or REST API calls, it's up to the client to generate a GUI from the information returned (if desired).

## Drawbacks

### Web Service Simulators

Executing the logon simulators that use the web service is impressive watching the web browser manipulate itself. However, the requirement of using a web browser was limiting in that executing **multiple concurrent applications** was not feasible. Options seemed to range from providing a list of applications to be tested (which are tested sequentially) or expanding the number of VM's that will run the logon simulator.  In addition, it's possible to create "Store" services that host applications without the corresponding "Web" service. Simulators that only test the Web Service will be unable to do any type of testing for these Stores.

#### Single VM

If you opt for a single VM to test as many applications as possible, the time between applications increases linearly. For 10 applications to be tested to validate they are operational, with a 2 minute interval you will only be testing that application every 20 minutes (at best). If you have 100 applications you'll be waiting over 3 hours between tests.

#### Multiple VM's

Multiple VM's operate very well with the web service. But they introduce another wrinkle. How feasible is it to expand your environment 100 VM's to test if 100 applications are operational? With a operating system overhead of ~1GB at best, you'll be consuming at least 100vCPU and 100GB more of memory. This is pretty much dedicating a host just for robots. This may be worth it when compared to the cost of time when a application is down... But this is now becoming a very, very expensive solution. Hardware costs for hosts, hypervisor licenses, and Windows licenses compound to the pain of a multi-VM solution.

### Store Service Simulators

In terms of a demo, the store service is less _visually_ impressive. The automation is done programmatically so you don't get to see those gratifying "clicks". However, there is no browser requirement. This does mean you don't need a full GUI operating system to run a store service logon simulator. In addition, because the Store Service simulator operates via API calls, it's completely possible to run multiple _**in**_ **_parallel_**. This means there is a very large opportunity to save VM costs by consolidating all the testing into one, or just a couple VM's and for each test it will keep that tight interval.  However, the draw back of the Store Service simulator is additional configuration is required for testing through a Netscaler.  Essentially, if you have not setup [the DNS SRV record](https://www.citrix.com/blogs/2013/04/01/configuring-email-based-account-discovery-for-citrix-receiver/) to allow Store Service communication than a Store Service simulator will not work externally.

## What's the plan?

After exploring these considerations, I set out to design something with some goals.

  1. Minimize the number of VM's to run the robots
  2. As little resource consumption as possible
  3. Still provide operational alerts
  4. Operate on-premise

[My next post will explore how to achieve this and the solution I settled on](https://theorypc.ca/2019/02/06/citrix-logon-simulators-part-2/).

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
