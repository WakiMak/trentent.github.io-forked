---
id: 2628
title: 'Citrix Local Host Cache &#8211; Performance Evaluation &#8211; 2017 edition'
date: 2017-11-27T12:06:11-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/11/27/2579-revision-v1/
permalink: /2017/11/27/2579-revision-v1/
---
Citrix introduced a feature with XenApp/XenDesktop 7.7 called &#8220;Local Host Cache&#8221; (LHC).Â When this feature was released there were some limitations but as time wore on and Citrix began to understand the technology and limitations were removed or reduced.

There are still some limitations on the technology compared to 6.5 IMA LHC, but it looks much better today than a year ago, and undoubtedly it will be better a year from now.Â However, the documented limitations of the Local Host Cache has me quite curious.

As an example, going through the Local Host Cache documentation, one of the big &#8220;limitations&#8221; is the number of VDA&#8217;s.Â When LHC was introduced the limit was 5,000 VDA&#8217;s, but there was never a **limit on the number of XenApp sessions**.Â The closest information I could find was [this old LHC sizing guide](https://docs.citrix.com/en-us/categories/solution_content/implementation_guides/local-host-cache-sizing-scaling.html) that is now out of date.Â In it, the author tested 100,000 XenApp users and 5075 VDI VDA&#8217;s.Â His findings for XenApp sessions were difficult to parse if only because the raw data is not available, but still, some information can be gleaned from the images.

<img class="size-large aligncenter" src="https://docs.citrix.com/content/dam/docs/en-us/categories/advanced-concepts/media/lhc-time-to-logon.png" width="725" height="424" /> 

Joe states the following:

<div class="richtext base section">
  <blockquote>
    <p>
      The &#8216;theoretical min&#8217; row is the absolute minimum time 100,000 users would take to log on if the environment was able to process 20 launches per second, giving 1 hour 23 minutes 20 seconds. Â In these tests, the 0 applications row managed 1:30:57 in the 6 vCPU case, and 1:30:48 in the 8 vCPU case. Â The performance of the Active Directory domain will have some impact on how quickly users are authenticated.
    </p>
  </blockquote>
  
  <p>
    We can kind of see that enumerating 400 applications at 20 concurrent requests per second (8/LHC on) appears to have taken 1:55:12.
  </p>
</div>

I have a couple concerns regarding the article.

The first concern is the testing was done using a very old processor, a AMD 8431 that was released June 1st 2009. Â This processor was 6 and a half years old when the article came out.Â The processor he was using has a [single thread rating of 854](https://www.cpubenchmark.net/cpu.php?cpu=AMD+Opteron+8431&cpuCount=4), a CPU 3 years newer ([Intel E5-2680,](https://ark.intel.com/products/64583/Intel-Xeon-Processor-E5-2680-20M-Cache-2_70-GHz-8_00-GTs-Intel-QPI)Â &#8212; still quite old!) scores a single thread performance of 1674.Â Nearly 2x the performance! Â It would be extremely handy to understand the actual performance limitation of LHC in a few CPU scenario&#8217;s (low, medium and high performing tiers).Â I would imagine that this is more realistic today than deciding that your controllers will live on in hardware that is sorely out of date.

The second concern, he tested with 7.12. Â There is nothing wrong with that itself as that was the release at the time he tested, but [7.14 brought dramatic performance improvements to LHC](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-14/manage-deployment/local-host-cache.html). Â Enough improvements that a **2x** **VDA density was achieved for a single zone** and **8x** **the number of VDA&#8217;s for a site**! Â Retesting with the improved LHC would have been nice and is even more important now because these improvements are in the 7.15 LTSR and people maybe sizing their brokers and/or farms with grossly incorrect assumptions.Â Does the VDA density matter for XenApp workloads?Â I don&#8217;t actually know but the improvements are dramatic and might apply.

I hadÂ [experimented with testing the performance of the brokers at enumerating applications](https://theorypc.ca/2017/04/13/citrix-xenapp-enumeration-performance-ima-vs-fma-oddities/) (400 applications actually!) on XenApp 7.13 _-without LHC-,_ and I found I could enumerate the applications at a crazy concurrent rate of 200/sec and it completed all requests in less than 1000ms. Â To offer some real world perspective on a fair-sized environment (concurrent user count of ~14,500), I examined the peak user logon rate and the rate wasÂ **~14/sec** during peak logon time.Â This means that over a 15 minute timespan in the morning, this 6.5 environment satisfies 12,600 XenApp logons.Â When I tested a 7.X playground environment, it stayed in lockstep with 6.5 until it _outperformed_ it when pushed with extremely high concurrent logon rates.

The FMA brokering performance for XenApp sessions seems to be extremely efficient, and performs better over larger loads than IMA.Â But what if your in a state that requires LHC?Â How does that look day?

I&#8217;m going to test the LHC performance on 3 classes of processors.

<div>
  Low Performance Tier, <a href="https://ark.intel.com/products/64595/Intel-Xeon-Processor-E5-2670-20M-Cache-2_60-GHz-8_00-GTs-Intel-QPI">Intel E5-2670, 2.60GHz Sandy Bridge (Released Q1 2012)</a>, <a href="https://www.cpubenchmark.net/cpu.php?cpu=intel+xeon+e5-2670+%40+2.60ghz">CPUMark single thread rating 1587</a>.
</div>

<div>
  Mid Performance Tier, <a href="https://ark.intel.com/products/91772/Intel-Xeon-Processor-E5-2660-v4-35M-Cache-2_00-GHz">Intel E5-2660 v4 2.00GHz Broadwell (Released Q1 2016)</a>, <a href="https://www.cpubenchmark.net/cpu.php?cpu=Intel+Xeon+E5-2660+v4+%40+2.00GHz">CPUMark single thread rating 1826</a>.
</div>

<div>
  High Performance Tier, <a href="https://ark.intel.com/products/91770/Intel-Xeon-Processor-E5-2690-v4-35M-Cache-2_60-GHz">Intel E5-2690 v4 2.60GHz Broadwell (Released Q1 2016)</a>, <a href="https://www.cpubenchmark.net/cpu.php?id=2780">CPUMark single thread rating 1927</a>.
</div>

In order to remove storage from factoring into this testing I&#8217;m going to put the [LHC database on a 1.5GB RAMDisk](https://blogs.technet.microsoft.com/windowsinternals/2017/08/25/how-to-create-a-ram-disk-in-windows-server/).

I&#8217;ve configured my Broker VM for best performance.Â The broker will be configured as a **2 socket, 8 core system (16 vCPU total)**. Â The host&#8217;s this VM will reside on will have 2 sockets with their respective processors with no other VM&#8217;s residing on them with hyper-threading enabled.Â With LHC on, the theory is 4 of those cores will go to the SQL Server Express instance. Â The VM will have 8GB RAM.Â The VM will be NUMA aware.

In order to test performance, I&#8217;m going to use WCAT to spin up a fixed number of _concurrent_ application enumeration requests against the broker.

Citrix has some excellent performance counters that measure the load against the LHC. Â &#8220;**Citrix High Availability XML Service &#8211; Concurrent Transactions**&#8221; matched the load wcat was reporting, so this is excellent! Â It means that the number of user enumeration requests was spot on.Â The counter &#8220;**Citrix High Availability XML Service &#8211; Avg. Transaction Time**&#8221; measured how long it took before I got back a response for the requests. Â With these two counters I can measure my load and how long it took for my XenApp application enumeration request to complete.

I configured wcat to add 10 concurrent connections every minute up to 10 minutes, to a maximum of 100 concurrent users requesting enumeration.Â Why concurrent?Â Well, that&#8217;s just what wcat does.Â However, concurrent testing makes this test very different compared to what was described in the Citrix test.Â The Local Host Cache Sizing article states testing was not at a fixed concurrent amount, but at a rate.Â That rate was 20 enumeration attempts _per second_.Â My testing at 20 enumeration attempts per second shows that the LHC, on these processors, chew through them like they are nothing.Â If the processor can finish the task before the rate (per second in this instance) then you&#8217;ll just come up with the &#8220;theoretical limit&#8221;.Â Example:

Each packet was processed before the &#8220;second&#8221; was complete, thus the performance will always be at the &#8220;theoretical limit&#8221; unless the processing time exceeds the &#8220;rate&#8221;.

<img class="aligncenter size-full wp-image-2585" src="http://theorypc.ca/wp-content/uploads/2017/11/Persecond.png" alt="" width="1406" height="271" srcset="http://theorypc.ca/wp-content/uploads/2017/11/Persecond.png 1406w, http://theorypc.ca/wp-content/uploads/2017/11/Persecond-300x58.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/Persecond-768x148.png 768w" sizes="(max-width: 1406px) 100vw, 1406px" /> 

Testing concurrent enumeration, however, we can process _**more**_Â transactions per second because concurrency means there are always 5 enumerations requests occurring.Â If these transactions take less than a second then more requests can be processed per second.

<img class="aligncenter size-full wp-image-2586" src="http://theorypc.ca/wp-content/uploads/2017/11/Concurrent.png" alt="" width="847" height="267" srcset="http://theorypc.ca/wp-content/uploads/2017/11/Concurrent.png 847w, http://theorypc.ca/wp-content/uploads/2017/11/Concurrent-300x95.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/Concurrent-768x242.png 768w" sizes="(max-width: 847px) 100vw, 847px" /> 

In my simplified examples, the 5 enumeration request each second will only do 5 requests in a single second interval.Â For the concurrent enumeration requests, the range is 9-10 requests per second.Â Twice the work accomplished!

Can we find out how many concurrent enumeration requests are completed in a given second then?

Yes!Â Citrix offers a 3rd counter that I will key in on.Â &#8220;**Citrix High Availability XML Service &#8211; Transactions/sec**&#8220;.Â This counter will tell me how many requests were completed in a given second.Â This counter provides me with an actual count of the &#8220;real work.&#8221;

Here is an image of the raw data:

<img class="aligncenter size-large wp-image-2582" style="text-align: center;" src="http://theorypc.ca/wp-content/uploads/2017/11/Screen-Shot-2017-11-17-at-2.20.58-PM-1600x855.png" alt="" width="1140" height="609" srcset="http://theorypc.ca/wp-content/uploads/2017/11/Screen-Shot-2017-11-17-at-2.20.58-PM-1600x855.png 1600w, http://theorypc.ca/wp-content/uploads/2017/11/Screen-Shot-2017-11-17-at-2.20.58-PM-300x160.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/Screen-Shot-2017-11-17-at-2.20.58-PM-768x410.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

The red line is the number of transactions done per second, the green line is the concurrent number of users requesting enumeration and the blue line is how long it took to satisfy each request.

The raw data:

<table class=" aligncenter" style="height: 386px;" width="947">
  <tr>
    <td width="159">
    </td>
    
    <td width="140">
      <span style="color: #800000;">E5-2670</span>
    </td>
    
    <td width="144">
    </td>
    
    <td width="111">
      <span style="color: #808000;">E5-2660v4</span>
    </td>
    
    <td width="144">
    </td>
    
    <td width="111">
      <span style="color: #0000ff;">E5-2690v4</span>
    </td>
    
    <td width="144">
    </td>
  </tr>
  
  <tr>
    <td>
      Concurrent requests
    </td>
    
    <td>
      <span style="color: #800000;">Transactions/sec</span>
    </td>
    
    <td>
      <span style="color: #800000;">Avg. Transaction Time (ms)</span>
    </td>
    
    <td>
      <span style="color: #808000;">Transactions/sec</span>
    </td>
    
    <td>
      <span style="color: #808000;">Avg. Transaction Time (ms)</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">Transactions/sec</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">Avg. Transaction Time (ms)</span>
    </td>
  </tr>
  
  <tr>
    <td>
      10
    </td>
    
    <td>
      <span style="color: #800000;">45.41884982</span>
    </td>
    
    <td>
      <span style="color: #800000;">212.1025433</span>
    </td>
    
    <td>
      <span style="color: #808000;">81.37907875</span>
    </td>
    
    <td>
      <span style="color: #808000;">117.0314831</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">104.1664581</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">94.58304208</span>
    </td>
  </tr>
  
  <tr>
    <td>
      20
    </td>
    
    <td>
      <span style="color: #800000;">45.06658696</span>
    </td>
    
    <td>
      <span style="color: #800000;">439.0654575</span>
    </td>
    
    <td>
      <span style="color: #808000;">78.93333422</span>
    </td>
    
    <td>
      <span style="color: #808000;">251.1161924</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">104.8795065</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">187.3677897</span>
    </td>
  </tr>
  
  <tr>
    <td>
      30
    </td>
    
    <td>
      <span style="color: #800000;">36.41660588</span>
    </td>
    
    <td>
      <span style="color: #800000;">824.0843186</span>
    </td>
    
    <td>
      <span style="color: #808000;">65.67668397</span>
    </td>
    
    <td>
      <span style="color: #808000;">458.6470345</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">78.37808685</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">386.0522131</span>
    </td>
  </tr>
  
  <tr>
    <td>
      40
    </td>
    
    <td>
      <span style="color: #800000;">33.6832376</span>
    </td>
    
    <td>
      <span style="color: #800000;">1187.350383</span>
    </td>
    
    <td>
      <span style="color: #808000;">63.71642739</span>
    </td>
    
    <td>
      <span style="color: #808000;">636.0645527</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">74.79906537</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">546.612349</span>
    </td>
  </tr>
  
  <tr>
    <td>
      50
    </td>
    
    <td>
      <span style="color: #800000;">31.48328618</span>
    </td>
    
    <td>
      <span style="color: #800000;">1584.367745</span>
    </td>
    
    <td>
      <span style="color: #808000;">53.93323804</span>
    </td>
    
    <td>
      <span style="color: #808000;">919.7082417</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">70.31665937</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">709.232745</span>
    </td>
  </tr>
  
  <tr>
    <td>
      60
    </td>
    
    <td>
      <span style="color: #800000;">32.84991648</span>
    </td>
    
    <td>
      <span style="color: #800000;">1806.315163</span>
    </td>
    
    <td>
      <span style="color: #808000;">54.30730723</span>
    </td>
    
    <td>
      <span style="color: #808000;">1100.968744</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">64.83313495</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">931.3893972</span>
    </td>
  </tr>
  
  <tr>
    <td>
      70
    </td>
    
    <td>
      <span style="color: #800000;">31.44991974</span>
    </td>
    
    <td>
      <span style="color: #800000;">2233.383315</span>
    </td>
    
    <td>
      <span style="color: #808000;">53.36657651</span>
    </td>
    
    <td>
      <span style="color: #808000;">1295.908498</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">66.74986539</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">1031.437336</span>
    </td>
  </tr>
  
  <tr>
    <td>
      80
    </td>
    
    <td>
      <span style="color: #800000;">30.46667135</span>
    </td>
    
    <td>
      <span style="color: #800000;">2553.245907</span>
    </td>
    
    <td>
      <span style="color: #808000;">51.53109524</span>
    </td>
    
    <td>
      <span style="color: #808000;">1533.056509</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">65.91644596</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">1209.430939</span>
    </td>
  </tr>
  
  <tr>
    <td>
      90
    </td>
    
    <td>
      <span style="color: #800000;">29.58324755</span>
    </td>
    
    <td>
      <span style="color: #800000;">3105.931052</span>
    </td>
    
    <td>
      <span style="color: #808000;">52.54982264</span>
    </td>
    
    <td>
      <span style="color: #808000;">1705.29403</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">58.84991703</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">1568.117465</span>
    </td>
  </tr>
  
  <tr>
    <td>
      100
    </td>
    
    <td>
      <span style="color: #800000;">28.1666105</span>
    </td>
    
    <td>
      <span style="color: #800000;">3610.460116</span>
    </td>
    
    <td>
      <span style="color: #808000;">51.45013343</span>
    </td>
    
    <td>
      <span style="color: #808000;">1932.482214</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">55.3165561</span>
    </td>
    
    <td>
      <span style="color: #0000ff;">1911.875756</span>
    </td>
  </tr>
</table>

&nbsp;

&nbsp;

<p style="text-align: center;">
  And now, the pretty graphs:
</p>

<div id="attachment_2594" style="width: 1225px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2594" class="wp-image-2594 size-full" src="http://theorypc.ca/wp-content/uploads/2017/11/averageTransactionTime.png" alt="" width="1215" height="855" srcset="http://theorypc.ca/wp-content/uploads/2017/11/averageTransactionTime.png 1215w, http://theorypc.ca/wp-content/uploads/2017/11/averageTransactionTime-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/averageTransactionTime-768x540.png 768w" sizes="(max-width: 1215px) 100vw, 1215px" /></p> 
  
  <p id="caption-attachment-2594" class="wp-caption-text">
    Average Transaction Time. Lower is better
  </p>
</div>

&nbsp;

<div id="attachment_2592" style="width: 1225px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2592" class="wp-image-2592 size-full" src="http://theorypc.ca/wp-content/uploads/2017/11/numberOfTransactionsPerSecond.png" alt="" width="1215" height="856" srcset="http://theorypc.ca/wp-content/uploads/2017/11/numberOfTransactionsPerSecond.png 1215w, http://theorypc.ca/wp-content/uploads/2017/11/numberOfTransactionsPerSecond-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/numberOfTransactionsPerSecond-768x541.png 768w" sizes="(max-width: 1215px) 100vw, 1215px" /></p> 
  
  <p id="caption-attachment-2592" class="wp-caption-text">
    Number of transactions per second. Higher is better
  </p>
</div>

&nbsp;

<img class="aligncenter size-full wp-image-2593" src="http://theorypc.ca/wp-content/uploads/2017/11/allOptionsOverlayed.png" alt="" width="1215" height="855" srcset="http://theorypc.ca/wp-content/uploads/2017/11/allOptionsOverlayed.png 1215w, http://theorypc.ca/wp-content/uploads/2017/11/allOptionsOverlayed-300x211.png 300w, http://theorypc.ca/wp-content/uploads/2017/11/allOptionsOverlayed-768x540.png 768w" sizes="(max-width: 1215px) 100vw, 1215px" /> 

&nbsp;

Per processor comparison:

<p style="text-align: center;">
  <div style="max-width: 1215px;" class="ml-slider-3-12-1 metaslider metaslider-flex metaslider-2589 ml-slider">
    <div id="metaslider_container_2589">
      <div id="metaslider_2589">
        <ul class="slides">
          <li style="display: block; width: 100%;" class="slide-2596 ms-image">
            <img src="http://theorypc.ca/wp-content/uploads/2017/11/E5-2670.png" height="855" width="1215" alt="" class="slider-2589 slide-2596" /> <div class="caption-wrap">
              <div class="caption">
                E5-2670
              </div>
            </div>
          </li>
          
          <li style="display: none; width: 100%;" class="slide-2598 ms-image">
            <img src="http://theorypc.ca/wp-content/uploads/2017/11/E5-2660v4.png" height="855" width="1215" alt="" class="slider-2589 slide-2598" /> <div class="caption-wrap">
              <div class="caption">
                E5-2660v4
              </div>
            </div>
          </li>
          
          <li style="display: none; width: 100%;" class="slide-2599 ms-image">
            <img src="http://theorypc.ca/wp-content/uploads/2017/11/E5-2690v4.png" height="855" width="1215" alt="" class="slider-2589 slide-2599" /> <div class="caption-wrap">
              <div class="caption">
                E5-2690v4
              </div>
            </div>
          </li>
        </ul>
      </div></p>
    </div>
  </div>
  
  <p>
    Satisfying a theoretical 100,000 XenApp users at 20 concurrent requests would take:
  </p>
  
  <table class=" aligncenter" width="208">
    <tr>
      <td width="144">
        Â E5-2690v4
      </td>
      
      <td width="64">
        15:53
      </td>
    </tr>
    
    <tr>
      <td>
        Â E5-2660v4
      </td>
      
      <td>
        21:06
      </td>
    </tr>
    
    <tr>
      <td>
        Â E5-2670
      </td>
      
      <td>
        36:58
      </td>
    </tr>
  </table>
  
  <p>
    So what is all of this information telling us?Â Knowing your peak concurrent rate is important.Â Ensuring your VM that will be your LHC is configured correctly and is on the best hardware possible will help ensure you have the best possible performance in the event you need to use your LHC.Â And even if your not using LHC, it plain and simply helps with brokering as fast as possible.
  </p>
  
  <p>
    These are very surprising results!Â The performance processor is over 2x faster than the low performance tier!Â I wasn&#8217;t expecting such a discrepancy when you look at the CPUMark numbers.
  </p>
  
  <p>
    The LHC does appear to operate at optimum performance atÂ 20 concurrent connections or less.Â Citrix offers a registry key to govern LHC to a maximum number of <span style="text-decoration: underline;"><strong>concurrent</strong></span> connections.
  </p>
  
  <pre class="lang:reg decode:true">HKEY_LOCAL_MACHINE\Software\Citrix\DesktopServer\ThrottledRequestAddressMaxConcurrentTransactions
DWORD</pre>
  
  <p>
    But during my testing I found this key to have no effect.Â I procmon&#8217;ed the registry path this key exists at and it was never read, regardless whether the value was present or not.
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <!-- AddThis Advanced Settings generic via filter on the_content -->
  
  <!-- AddThis Share Buttons generic via filter on the_content -->