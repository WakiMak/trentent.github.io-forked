---
id: 1615
title: About the XenApp 6.5 Group Policy Client Side Extensions (CSE)
date: 2016-08-02T21:13:28-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1615
permalink: /2016/08/02/about-the-xenapp-6-5-group-policy-client-side-extensions-cse/
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - printing
  - XenApp
---
> <p class="">
>   TLDR; using a newer Citrix Group Policy Management (GPM) than 1.7.X on XenApp 6.5 will cause your policies to disappear if you upgrade your Client Side Extensions to a version higher than 1.7.6.
> </p>

The Citrix Client Side Extension (CSE) are the &#8216;Citrix Group Policy Engine&#8217;. Â The CSE takes whatever policies you set through Active Directory, locally or the AppCenter console and apply them to your server or Citrix session (if a user policy). Â There is some oddity with the CSE and &#8216;Citrix Group Policy Management&#8217; portions of the Citrix products. Â You see, they are interoperable, but in certain scenario&#8217;s they are not.

The [split appears to be for the XenApp 6.5 product for CSE 1.7.6+](https://theorypc.ca/2016/07/14/citrix-client-side-extensions-1-7-6-breaks-policies/). Â My Citrix TRM informed me that the _<span style="text-decoration: underline;">Active Directory</span>Â_ Group Policy Schema changed for CSE 1.7.6+. Â If you intend to use CSE 1.7.6+ you will need to upgrade your [Group Policy Mangement (GPM) to 1.7.11](http://support.citrix.com/article/CTX202233). Â To upgrade your AD policy seems simple enough. Â Citrix says open the policy on a computer with GPM 1.7.11 and then close it and it will become updated.

But here&#8217;s a bit of the rub. Â Citrix supports and encourages &#8220;some&#8221;Â mixing and matching of some components. Â [Specifically, the Citrix Universal Print Server (UPS) and Client](http://docs.citrix.com/en-us/xenapp-and-xendesktop/7-6/xad-print-landing/xad-print-provision.html)Â (UPC).

And here&#8217;s my story:

We wanted to use the 7.6 version of UPS/UPC as it had some improvements we deemed critical. Â We had not upgraded the version of the Citrix GPM/CSE from what came with the Citrix XenApp 6.5 (1.5.0). Â When we downloaded UPS/UPC 7.6 we found we could not configure the Group Policy Â settings for the Universal Print Client&#8230; Until we upgraded the GPM that came with XenApp 7.6. Â Then the UPM policies appeared, ready to configure. Â The version of GPM included with XA7.6 was 2.2.0.

Only on reboot, with the policies set, we found they were still not applying. Â At this time, I found you need version 1.7.0 of the CSE to recognize the new policies. Â We installed CSE 1.7.0 and it recognized all the policies and we were flying.

Fast forward a year or so later and we decided to &#8216;get up to date&#8217; with our operational software. Â Essentially, we wanted to ensure we had all the bug fixes enhancements of all of the latest and greatest for XA6.5 so we can survive for the next couple years while we transition to whatever Citrix will have out by then. Â So the latest and greatest [CSE is 1.7.6+ and I installed it, and all my policies went poof](https://theorypc.ca/2016/07/14/citrix-client-side-extensions-1-7-6-breaks-policies/). Â This prompted my earlier post.

During the course of troubleshooting my issue I installed various versions of the CSE&#8217;s and GPM&#8217;s that came with the various versions of Citrix XenApp. Â Since we had GPM 2.2.0 installed, nothing from the 1.7.6 CSE branch recognized any of the policies. Â BUT, installing any of the CSE&#8217;s from XA7.5+ recognized and applied the 6.5 policies and everything on top of that. Â So I started asking our Citrix TRM if it was supported to have the CSE from the newer XenApp 7+ on 6.5 and if they included all the policies. Â The answer was &#8216;Maybe it works, probably not supported&#8217;. Â So I asked why the policies of 2.2.0+ don&#8217;t work with CSE 1.7.6 and the answer I got was the schema changed for the GPO&#8217;s. Â This is implied in [CTX202233](http://support.citrix.com/article/CTX202233):

> _**Note**: This fix addresses the issue for AD policies you create after installing this update. It also addresses it for existing policies where Citrix settings were configured before Microsoft settings. It does not address it for existing AD policies where Microsoft settings were configured before Citrix settings. For those AD policies, <span style="text-decoration: underline;"><strong>you must open the affected policies and save the Citrix settings</strong></span>._

Opening and saving the policies updates the schema.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->