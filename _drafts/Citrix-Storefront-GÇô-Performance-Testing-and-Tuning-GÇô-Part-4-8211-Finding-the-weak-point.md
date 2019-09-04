---
id: 2354
title: 'Citrix Storefront â€“ Performance Testing and Tuning â€“ Part 4 &#8211; Finding the weak point'
date: 2017-05-27T22:28:46-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2354
permalink: /?p=2354
categories:
  - Uncategorized
---
So what does a PNA session look like for Receiver?

# 

# To Web Interface

<img class="aligncenter size-large wp-image-2338" src="http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA.png" alt="" width="667" height="175" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA.png 667w, http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA-300x79.png 300w" sizes="(max-width: 667px) 100vw, 667px" /> 

<img class="aligncenter size-full wp-image-2337" src="http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA_WI2.png" alt="" width="638" height="173" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA_WI2.png 638w, http://theorypc.ca/wp-content/uploads/2017/05/Receiver4.7PNA_WI2-300x81.png 300w" sizes="(max-width: 638px) 100vw, 638px" /> 

Around 700 connections to WebInterface!

# To Storefront

<img class="aligncenter size-large wp-image-2340" src="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront.png" alt="" width="638" height="103" srcset="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront.png 638w, http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefront-300x48.png 300w" sizes="(max-width: 638px) 100vw, 638px" /> 

<img class="aligncenter size-full wp-image-2339" src="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefrontx2.png" alt="" width="648" height="101" srcset="http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefrontx2.png 648w, http://theorypc.ca/wp-content/uploads/2017/05/4.7PNA_Storefrontx2-300x47.png 300w" sizes="(max-width: 648px) 100vw, 648px" /> 

And a little under 500 to Storefront!

Each &#8216;enum.aspx&#8217; looks like it&#8217;s querying for the icon. Â This process appears to be **very**Â inefficient in Receiver as it appears to be done serially. Â Slowing waiting for each request to complete before sending the next one. Â It&#8217;s a bit weird because the large, initial Receiver receiptÂ (query #26) asks for the &#8220;DesiredIconData size=16&#8221; (16&#215;16?) icon data and gets a big dump. Â But then it continues to query for individual icons of different sizes (I think). Â Some of those next queries are asking for icons specifically at 32 or 48. Â Another oddity is the NFuseProtocol that is being posted by the 4.7 Receiver is stating that it&#8217;s a lower versionÂ (NFuseProtocol version=4.1&#8230;) than 3.4 Receiver as well . Â I&#8217;m not sure if that&#8217;s a problem or why, but there it is.

<div id="attachment_2341" style="width: 1337px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2341" class="wp-image-2341 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/47_IconData.png" alt="" width="1327" height="338" srcset="http://theorypc.ca/wp-content/uploads/2017/05/47_IconData.png 1327w, http://theorypc.ca/wp-content/uploads/2017/05/47_IconData-300x76.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/47_IconData-768x196.png 768w" sizes="(max-width: 1327px) 100vw, 1327px" /></p> 
  
  <p id="caption-attachment-2341" class="wp-caption-text">
    Citrix Receiver 4.7 initial icon query &#8211;> NFuseProtocol = 4.1
  </p>
</div>

&nbsp;

&nbsp;

<div id="attachment_2342" style="width: 1149px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2342" class="wp-image-2342 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/34_IconInfo_all.png" alt="" width="1139" height="371" srcset="http://theorypc.ca/wp-content/uploads/2017/05/34_IconInfo_all.png 1139w, http://theorypc.ca/wp-content/uploads/2017/05/34_IconInfo_all-300x98.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/34_IconInfo_all-768x250.png 768w" sizes="(max-width: 1139px) 100vw, 1139px" /></p> 
  
  <p id="caption-attachment-2342" class="wp-caption-text">
    Citrix Receiver 3.4 Initial Icon Query &#8211;> Desired details == all &#8211;> NFuseProtocol = 4.6
  </p>
</div>

I ran my test a couple times and found that sometimes I only got the 2 enum.aspx requests, sometimes I got hundreds.

So I started to wonder, does _when_Â does ReceiverÂ ask for all of these icons? Â To test I spun up a new machine, installed Fiddler and connected to a new PNA site. Â The result?

<img class="aligncenter size-full wp-image-2351" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-9.57.08-PM.png" alt="" width="1239" height="486" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-9.57.08-PM.png 1239w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-9.57.08-PM-300x118.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-9.57.08-PM-768x301.png 768w" sizes="(max-width: 1239px) 100vw, 1239px" /> 

Icons! Â Lots of icons!

Why did I get lots of icons this time?

I&#8217;m starting to wonder if this is a caching thing. Â To test I simply exited Reciever 3.4 and reopened it, connecting to the PNA site. Â Annnnnd&#8230;.

<img class="aligncenter size-full wp-image-2352" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.01.42-PM.png" alt="" width="660" height="159" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.01.42-PM.png 660w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.01.42-PM-300x72.png 300w" sizes="(max-width: 660px) 100vw, 660px" /> 

Look at that. Â Just the 3 requests and the 1 big icon request.

So it MUST be caching the icons. Â And for Receiver 3.4 it caches the icons here:

%userprofile%\AppData\Local\Citrix\PNAgent\Icon Cache

<img class="aligncenter size-full wp-image-2353" src="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.04.39-PM.png" alt="" width="777" height="471" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.04.39-PM.png 777w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.04.39-PM-300x182.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Screen-Shot-2017-05-27-at-10.04.39-PM-768x466.png 768w" sizes="(max-width: 777px) 100vw, 777px" /> 

So what happens if I delete them? Â Will it re-request them?

Sure enough. Â It does. Â And I delete an icon or a few icons it will send individual requests for them the next time I connect to the PNA site.

So now I&#8217;m starting to wonder&#8230; Â Is icon generation the hardest thing a XML broker has to do? Â Each of these requests are completely independent. Â They do not need to be done sequentially so I can just hammer an XML broker with an enum request or the config.xml request and see what breaks first!

However, in some prior monitoring we did see very short &#8216;bursts&#8217; of a concurrent connection rate of **400**Â **per second**. Â However, when we saw these bursts they were only in the span of a few minutes. Â At the time our infrastructure wasn&#8217;t sized to handle this manyÂ concurrent connections per second and users experienced a delay as their requests to the web interface was queued. Â It may not sound like this should be an issue, but from aÂ _users perspective_ if they logon to their computer and it takes a minute or two of their applications to appear, it&#8217;s a frustrating experience. Â Once we discovered this (the load issue would last for around 5 minutes&#8230; very hard to troubleshoot/detect such aÂ shortly lived transient issue) setting up testing and sizing was easy to do. Â As a matter of fact, the highest peak rate of server SITE3 for that hour (9510Â connections) had ~50% of those connections in the first 15 minutes of that hour! Â So my peak rate &#8220;hour rate&#8221; of (2.641*2.63 = 6.94583) grossly underestimates the actual user load. Â So it&#8217;s important to try and get highest amount of detail you can and try to avoid &#8220;averages&#8221; that can be skewed very heavily by extending a time sample. Â Since my experience has shown me what we actually experienced as a real &#8216;burst&#8217; peak concurrent rate vs our &#8216;hourly&#8217; current rate I would purpose, if you can get a peak &#8216;hourly&#8217; rate, **multiplying that by 20****Â** to get your &#8216;burst&#8217; peak rate of that hour. Â For PNA (if you use it). Â _The only caveat I&#8217;ll state is our organization doesn&#8217;t stagger work hours. Â The majority of our population starts at 7AM &#8212; sharp! Â Hence the spike of logons/traffic&#8230;_

In my experience, at least, this is a rule of thumb we&#8217;ll follow.

That said, if we were to combine all the PNA traffic from each of the 3 sites we get a peak hourly rate of 19.806, and if we multiply that by 20Â we get a peak &#8220;burst&#8221; concurrent rate of**Â ~400 PNA connections per second**. Â This is the number I am going to set as a minimum that our Storefront server must achieve. Â The reason why I&#8217;m going to size our Storefront server for this load is we try to ensure each site, each can take the full load of everything to try and ensure we have full redundancy across our organization. Â If, for whatever reason, we lost the StoreFront servers at sites 2 and 3, we&#8217;d want to ensure Site1 could handle the load of our user base.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->