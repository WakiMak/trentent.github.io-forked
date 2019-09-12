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

---
> <p class="">
>   TLDR; using a newer Citrix Group Policy Management (GPM) than 1.7.X on XenApp 6.5 will cause your policies to disappear if you upgrade your Client Side Extensions to a version higher than 1.7.6.
> </p>

The Citrix Client Side Extension (CSE) are the 'Citrix Group Policy Engine'.  The CSE takes whatever policies you set through Active Directory, locally or the AppCenter console and apply them to your server or Citrix session (if a user policy).  There is some oddity with the CSE and 'Citrix Group Policy Management' portions of the Citrix products.  You see, they are interoperable, but in certain scenario's they are not.

The [split appears to be for the XenApp 6.5 product for CSE 1.7.6+](https://theorypc.ca/2016/07/14/citrix-client-side-extensions-1-7-6-breaks-policies/).  My Citrix TRM informed me that the _<span style="text-decoration: underline;">Active Directory</span>_ Group Policy Schema changed for CSE 1.7.6+.  If you intend to use CSE 1.7.6+ you will need to upgrade your [Group Policy Mangement (GPM) to 1.7.11](http://support.citrix.com/article/CTX202233).  To upgrade your AD policy seems simple enough.  Citrix says open the policy on a computer with GPM 1.7.11 and then close it and it will become updated.

But here's a bit of the rub.  Citrix supports and encourages "some" mixing and matching of some components.  [Specifically, the Citrix Universal Print Server (UPS) and Client](http://docs.citrix.com/en-us/xenapp-and-xendesktop/7-6/xad-print-landing/xad-print-provision.html) (UPC).

And here's my story:

We wanted to use the 7.6 version of UPS/UPC as it had some improvements we deemed critical.  We had not upgraded the version of the Citrix GPM/CSE from what came with the Citrix XenApp 6.5 (1.5.0).  When we downloaded UPS/UPC 7.6 we found we could not configure the Group Policy  settings for the Universal Print Client... Until we upgraded the GPM that came with XenApp 7.6.  Then the UPM policies appeared, ready to configure.  The version of GPM included with XA7.6 was 2.2.0.

Only on reboot, with the policies set, we found they were still not applying.  At this time, I found you need version 1.7.0 of the CSE to recognize the new policies.  We installed CSE 1.7.0 and it recognized all the policies and we were flying.

Fast forward a year or so later and we decided to 'get up to date' with our operational software.  Essentially, we wanted to ensure we had all the bug fixes enhancements of all of the latest and greatest for XA6.5 so we can survive for the next couple years while we transition to whatever Citrix will have out by then.  So the latest and greatest [CSE is 1.7.6+ and I installed it, and all my policies went poof](https://theorypc.ca/2016/07/14/citrix-client-side-extensions-1-7-6-breaks-policies/).  This prompted my earlier post.

During the course of troubleshooting my issue I installed various versions of the CSE's and GPM's that came with the various versions of Citrix XenApp.  Since we had GPM 2.2.0 installed, nothing from the 1.7.6 CSE branch recognized any of the policies.  BUT, installing any of the CSE's from XA7.5+ recognized and applied the 6.5 policies and everything on top of that.  So I started asking our Citrix TRM if it was supported to have the CSE from the newer XenApp 7++ on 6.5 and if they included all the policies.  The answer was 'Maybe it works, probably not supported'.  So I asked why the policies of 2.2.0+ don't work with CSE 1.7.6 and the answer I got was the schema changed for the GPO's.  This is implied in [CTX202233](http://support.citrix.com/article/CTX202233):

> _**Note**: This fix addresses the issue for AD policies you create after installing this update. It also addresses it for existing policies where Citrix settings were configured before Microsoft settings. It does not address it for existing AD policies where Microsoft settings were configured before Citrix settings. For those AD policies, <span style="text-decoration: underline;"><strong>you must open the affected policies and save the Citrix settings</strong></span>._

Opening and saving the policies updates the schema.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->