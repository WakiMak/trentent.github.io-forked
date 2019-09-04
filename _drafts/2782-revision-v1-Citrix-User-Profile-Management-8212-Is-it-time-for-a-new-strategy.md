---
id: 2783
title: 'Citrix User Profile Management &#8212; Is it time for a new strategy?'
date: 2018-04-27T14:41:11-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2018/04/27/2782-revision-v1/
permalink: /2018/04/27/2782-revision-v1/
---
Being a Citrix Technology Advocate has some huge advantages. Â The biggest advantage I&#8217;ve experienced is the network of people you meet and converse along the way.

We had an issue with our profile server that had me re-examine profiles. Â We&#8217;ve never had a profile strategy before. Â We just used Citrix User Profile Management for everything, and eventually evolved UE-V into the solution as well. Â But there was no real logic or flow to why use one over the other or other solutions. Â I wrote some thoughts down, fed it to the community for feedback and got some immediately from [Phillip Jones](http://p2vme.com) and [James Rankin.](https://twitter.com/james____rankin)Â Can I just say, wow? Â Sanity checks from some of the smartest people there in realtime. Â ++ CTA.

So let&#8217;s talk User Profile Management. Â WHY do we have profiles? What problems do profiles solve?

When I started with a XenApp 6.5 migration project, profiles were originally required for Office. Storing the signatures/themes/customizations.

Initially, we didn&#8217;t really have any other application that stored information in a user profile.

However, that eventually changed as we onboarded more and more applications. When we started getting tickets about users losing their customizations we investigated and found that our helpdesk had a &#8220;Step 1 for Citrix problems&#8221;: Delete the users profiles

This lead us to re-examine a process for troubleshooting Citrix issues, specifically &#8220;Step 1&#8221;. We modified the helpdesk process. Instead of delete they would rename the profile so they could test to see if it fixed whatever problem they had. The problem, inevitably, is the problem wasn&#8217;t resolved but the profile wouldn&#8217;t get set back. So now our profile stores have thousands of renamed profiles sitting their doing nothing but taking up space. And our users had to redo their customizations anyways.

So it&#8217;s a process problem, right? Well, being techies, it&#8217;s always more interesting to solve a process problem with **Technology**!

Enter Microsoft User Experience-Virtualization (UE-V). With UE-V we could inject settings on application launch, long after profile load is complete. UE-V stores settings in a new location, and help desk can continue their same process of deleting profiles as step 1, but now the user isn&#8217;t impacted. UE-V is like a scalpel, it&#8217;s precise in what you capture so the size and amount of data is smaller than when capturing whole profiles.

But we still require a profile for some applications. UE-V has been successful for us for 99% of our applications, but we had a couple that we haven&#8217;t quite narrowed down what to capture, or the app crashes when UE-V does it&#8217;s injection.

At this point, we&#8217;ve identified some apps that require a profile, some that require UE-V, but what about apps that <span style="text-decoration: underline;"><strong>don&#8217;t</strong></span> require a profile?

It turns out about 70% of our apps do not require a profile of any kind! That is, a Mandatory profile would suffice just fine.

But why would you use a Manadatory profile?

I feel you&#8217;d use one if you needed to establish a consistent profile with some constants embedded in it. Which, we do not need. So then why Mandatory? Well, mandatory profiles get wiped off the server on logoff. For us, this is the only use case I can think of. Honestly, it&#8217;s a poor one. Also, you do not remove the dependency on several 3rd party components.

You still need a disk attached to a server and the network to get to that server to pull the mandatory profile. Â A single network problem, storage problem, DNS problem, etc. and your logons are delayed.

So what if we used local profiles instead? Our servers are PVS servers. So the profile will get loaded into disk cache on the PVS server on first read, since it&#8217;s small and local it should be super fast. It removes the tertiary components (other disk, server and network). But there isn&#8217;t a mechanism to remove a local profile on log off. Â James Rankin suggested tagging the profile so it&#8217;s no longer identified as local, but as another type so that on log off Microsoft will recognize as some form of profile that requires removal (eg, temporary or mandatory).

All of this said, I&#8217;ve put together a user profile strategy based on an order of preference:

1 &#8211; Non-persistent local profile

(if app requires settings capture)

2 &#8211; Non-persistent local profile with UE-V

(if UE-V fails to satisfy)

3 &#8211; Standard Citrix User Profile

1 & 2 can come free. UE-V only triggers on application or session launch if configured. #3 will be set as an exception policy via group membership. &#8220;If %USER% member of group &#8216;Citrix UPM User&#8217; &#8211;> Use UPM&#8221;.

How can we implement this? Â There are two technical challenges.

Tagging profiles as remove on log off.  
Use Citrix UPM if member of exception group.

UE-V requires no customization and can operate within this operational criteria for free ðŸ™‚

&nbsp;

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->