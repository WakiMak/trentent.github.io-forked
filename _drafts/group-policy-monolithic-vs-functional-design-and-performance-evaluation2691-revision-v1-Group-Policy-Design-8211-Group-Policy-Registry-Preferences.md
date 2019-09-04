---
id: 2694
title: 'Group Policy Design &#8211; Group Policy Registry Preferences'
date: 2018-04-01T23:16:14-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2018/04/01/2691-revision-v1/
permalink: /2018/04/01/2691-revision-v1/
---
Group Policy Design is a hotly discussed topic, with lots of different ideas and discussions. Â However, there is not a whole lot of actual metrics. Â ADM and ADMX templates apply registry keys in an &#8216;enforced&#8217; manner. Â That is, if you or the machine has access to read the policies, the registry keys within are applied. Â If you stuck purely to ADM/ADMX policies but wanted to do dynamic filtering you&#8217;d probably design multiple policies and nested organizational units (OUs). Â From here, you could filter certain policies based on the machine or user location in the OU structure or by filtering on the policies themselves and denying access to certain groups or doing explicit allows to certain groups. Â This design style, back in the day, was called &#8220;[functional](http://www.itprotoday.com/management-mobility/group-policy-design-best-practices)&#8221; design.

<img class="aligncenter size-full wp-image-2692" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-01-at-10.48.20-PM.png" alt="" width="418" height="595" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-01-at-10.48.20-PM.png 418w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-01-at-10.48.20-PM-211x300.png 211w" sizes="(max-width: 418px) 100vw, 418px" /> 

&nbsp;

However, the alternative style,Â &#8220;monolithic&#8221; design, simplifies the group policy object (GPO) design into much fewer GPO&#8217;s.

<img class="aligncenter size-full wp-image-2693" src="http://theorypc.ca/wp-content/uploads/2018/04/Monolthic.png" alt="" width="387" height="213" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Monolthic.png 387w, http://theorypc.ca/wp-content/uploads/2018/04/Monolthic-300x165.png 300w" sizes="(max-width: 387px) 100vw, 387px" /> 

&nbsp;

Within these GPO&#8217;s

&nbsp;

This style of design can work if you haveÂ _**ultra**__Â_ low latency to your domain controllers. Â One of the more intriguing issues I&#8217;ve experienced with a functional design is old axiom, you&#8217;re only as fast as your slowest part. Â Even with domain controllers residing in the millisecond or so latency away from my terminals,Â _**latency**__Â_ is still the largest contributor to overall time spent per GPO.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->