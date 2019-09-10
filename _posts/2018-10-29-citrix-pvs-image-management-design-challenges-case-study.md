---
id: 2829
title: Citrix PVS Image Management Design Challenges - Case Study
date: 2018-10-29T13:51:13-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2829
permalink: /2018/10/29/citrix-pvs-image-management-design-challenges-case-study/
categories:
  - Blog
---
## Challenge:

Different applications have differing levels of operating system support.&nbsp; A vendor might only support their app on a specific operating system, or multiple /older/ operating systems, or the organization hasn't updated to a new version that supports the new OS; whether because the new version has a cost attached to upgrading the org cannot afford at this time, the people that can assist the upgrade are tied up in other projects, or the org has lowered the priority of the app because of low user count and no impact to the app functionality outside of newer OS support.&nbsp; Whatever the situation, inevitably, someone will come to the Citrix team and ask that these applications be made available.&nbsp; And, in all honesty, Citrix has made this all but possible with the 7.1x product.&nbsp; The LTSR of &/XenDesktop supports 2008R2 for the next&nbsp;4 years, and 2012R2 and 2016 will continue to support the latest release until the next LTSR where 2012R2 will probably receive another 5 year reprieve from the Citrix side.&nbsp; You probably won't have Microsoft support, but that doesn't stop organizations from continuing to run Server 2003 for apps that can't move beyond that platform.

## What does this mean:

Application support requirements now necessitate multiple OS's available thru Citrix.&nbsp; The goal is to have the app on the highest supported OS.&nbsp; For instance, if an app is supported on 2008R2 and 2012R2, have the app run on 2012R2.&nbsp; By <span style="text-decoration: underline;"><strong>December 2018</strong></span>, this now means supplying the organization the opportunity and flexibility to have their app on a choice of&nbsp;<span style="text-decoration: underline;"><strong>"5"</strong></span> different operating systems:  
2008R2  
2012R2  
2016  
2019  
Windows 10 ERS

All on the & 7.1x platform.

By providing these choices, we should be ensuring maximum application compatibility and support from the vendor, and future proofing.

## Current State:

The original design of our environment was a single image, and using layering technologies to add software without causing compatibility issues.&nbsp; The technology of choice was AppV (version 4.6 as of the original design).

It was simple, it would be effective, and it would be easy to manage.

Using machine group memberships, AppV layers could be customized for the machine.&nbsp; For instance, we could have pools of servers that have different personalities.&nbsp; We had a "Generic" pool upon which 200 or so AppV applications would reside and if a new application request came down, instead of silo'ing a & VM for 3-10 users, we could put them on the generic pool and grow the generic pool if required.&nbsp; This is very cost effective, reduces overhead on the hosts by reducing the number of VM's, reduces the number of uniquely sized VM's and overall made management easier.&nbsp; Utilizing AppV it became trivial to have multiple applications, even the&nbsp;**exact same** versions of the applications but different configurations available for users to run from the same VM.&nbsp; However, we have large user-based applications with specific performance goals and monitored metrics that are easier to mange when they run in their own silo.&nbsp; And with AppV we could still use that same image but apply only the specific AppV packages and have the machines fully unique for those users.&nbsp; And from there it again became trivial to manage the single image.

Utilizing this model has reduced and kept our image count low while maintaining a high degree of flexibility.&nbsp; We have servers targeted for specific and unique purposes or servers hosting applications that are more general in nature.&nbsp; By reducing the amount of different VM configurations running on a variety of different hosts, we are able to fairly accurately predict our performance and thus plan accordingly for sizing.

## Are we really on one image?

No.&nbsp; We have 16 of them.

Although one image was the original design goal, required OS features crippled our ability to maintain a low image count.&nbsp; The worst offenders were applications with specific Internet Explorer version requirements.&nbsp; Being unable to run multiple versions of IE on a single image meant we had to split our single image.&nbsp; And we ended up splitting it into 3: images with IE9, IE10, and IE11.&nbsp; To top it further off, when we originally went through the design with AppV, AppV5.0 had just come out and had crippling limitations.&nbsp; AppV 4.6 was still the preferred platform while AppV5 matured.&nbsp; Eventually, we had a couple applications that would not work on 4.6 due to hitting the package size limit (2GB IIRC) or due to crashing when running within the bubble.&nbsp; AppV5.0 at the time was just plain broken and a huge number of our apps just outright failed.&nbsp; Due to these limitations, our decision was to create new images; branching the 3 images even further to a total of 8.

### So how did you get to 16?

We have a completely separate test environment.&nbsp; Once an image was created, it was duplicated for test.&nbsp; The design was to ensure any application upgrades, component upgrades, Windows Updates, policy changes, troubleshooting, etc would be done in the test environment first before being implemented in prod.&nbsp; This has worked fairly well, however we encountered something that caused our test environment to not be reflective of production:&nbsp;<span style="text-decoration: underline;"><strong>TIME</strong></span><span style="text-decoration: underline;">.</span>

One of the challenges we have faced was the testing of applications or components or tweaks lasting months.&nbsp; Testing a fix/feature/upgrade usually followed this path:

<figure class="wp-block-image"><img src="/wp-content/uploads/2018/08/FixFlow.png" alt="" class="wp-image-2831" srcset="/wp-content/uploads/2018/08/FixFlow.png 837w, /wp-content/uploads/2018/08/FixFlow-300x266.png 300w, /wp-content/uploads/2018/08/FixFlow-768x681.png 768w" sizes="(max-width: 837px) 100vw, 837px" /></figure> 

The challenges in this process occurs if the development of fixes/upgrade/enhancements spans multiple image "baking" sessions over a time period of weeks or months. It seems no matter how good you think your documentation is, if the development spans 10's to 100's of tweaks over sufficient time, something important will be lost in the chain. The process of enabling the change into production followed this process:

<figure class="wp-block-image"><img src="/wp-content/uploads/2018/08/ReleaseToProductionFlow-1.png" alt="" class="wp-image-2833" srcset="/wp-content/uploads/2018/08/ReleaseToProductionFlow-1.png 1615w, /wp-content/uploads/2018/08/ReleaseToProductionFlow-1-273x300.png 273w, /wp-content/uploads/2018/08/ReleaseToProductionFlow-1-768x844.png 768w, /wp-content/uploads/2018/08/ReleaseToProductionFlow-1-1456x1600.png 1456w" sizes="(max-width: 1615px) 100vw, 1615px" /></figure> 

### Couldn't you just copy test into production?

In the original design test and production were wholly separate and lived apart. The process of documenting and pushing changes from test into prod actually worked quite well until we started encountering the lengthy development/test change time scales, and numerous /different/ changes that occur across the different images. Copying from test into production was done a few times and painful results each time. The original image and design was not done to accommodate such a scenario so items that needed to be updated from test to prod were numerous and onerous. Scripts, registry, files, group policies... during some of these copies outages were incurred, again, because something was inevitably missed. At this point, trying to flush out all the different changes that would have to be done across 8 images and than maintaining the list was fairly daunting for our team. Having outages is a really, really bad thing.

### What's wrong with 16 images?

Our images have branched from each other in various forms going back to that first image created around 2011. These images all have lineages 7 years old and with age comes problems. We have been finding that these images have had some corruption in some form or another. Whether it's a registry key that's lost its ACL or is completely corrupted, Windows Updates that fail to install, a file whose content is now garbage as opposed to clean text, or the complete inability to uninstall or upgrade some software packages. Thus far, none of these problems have been unsolvable, but the fact they are cropping with more regularity across all of our images is very worrisome. The time taken to resolve these issues as they occur is getting excessive and more common. When we looked at just updating these images for XenDesktop 7 we found we were unable to do so because & 6.5 would not uninstall from some of them!

### Last word on "Current State"

Being a large environment, we could have several application/components simultaneously under this test/development process; compounding the number of changes.

Because of a separation of test and production the amount of changes during the development stage targeting test servers/components/configs inevitable have something get lost/forgotten/missed. Copying test images to production is untenable in this process. So many hard-coded pieces embedded within the test images or configurations that need to be updated that when we \*have\* had to a test to prod copy things break and a lot of effort goes into tracking these additional hard-coded changes.

All of these issues exist within just a single OS, with a single version of Citrix. The future will be multi-OS with multiple versions of the Citrix VDA. Our current design is unfeasible if the various OS's start encountering similar, unique image requirements. And it may be naive to think we can avoid it. But I believe we can put forth a design to make this more manageable, more predictable and more stable.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
