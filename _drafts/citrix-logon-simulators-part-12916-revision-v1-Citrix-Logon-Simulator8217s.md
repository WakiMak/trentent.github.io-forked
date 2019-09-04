---
id: 2919
title: 'Citrix Logon Simulator&#8217;s'
date: 2019-01-29T12:53:47-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2019/01/29/2916-revision-v1/
permalink: /2019/01/29/2916-revision-v1/
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

&nbsp;

I haven&#8217;t found a logon simulator that tests each service, the products out there only test one or the other.Â Another challenge I found is the simulators that test the Web service. That said, one of the challenges I found when testing them was the amount of resources required to run them.Â I&#8217;m talking about dedicated VM&#8217;s to run these simulations.Â I want to run the simulator on premise with an absolute minimal amount of resources and VM&#8217;s.Â When examining the various solutions I set forth a set of requirements:

  * Must work on Server Core
  * Must be able to launch multiple sessions to different simultaneously without the need for multiple logons
  * 

One of the challenges of a Citrix Logon Simulator is you need significant resources to host this software.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->