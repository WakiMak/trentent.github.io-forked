---
id: 1441
title: Failures with Sequencing AppV5
date: 2016-04-28T23:54:11-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/04/28/529-revision-v1/
permalink: /2016/04/28/529-revision-v1/
---
I sequenced an application (Epic 2014 Client Pack 12) and I publish out for our users. &nbsp;Calls start coming in that users are seeing error messages within the application. &nbsp;Doing some procmon traces I can see registry key are showing &#8216;InProcServer&#8217; missing. &nbsp;I found the DLL associated with this error message and ran a &#8216;regsvr32 dllName.dll&#8217; while in the virtual environment and the error was resolved. &nbsp;So I re-opened the package in the sequencer and did the &#8216;regsvr32 dllName.dll&#8217;, saved the package, and republished it out.

Users then found another error. &nbsp;And I did the regsvr32 again.

Then they found another error.

And another.

And another.

At this point I decided to just resequence this application. While sequencing I launched the program and tried a couple of the failure scenarios on the sequencer and the program worked perfectly. &nbsp;I then published this application back out to the users to test.

And those same failure scenarios I tested on the sequencer&#8230; &nbsp;They appeared when published. &nbsp;So what&#8217;s going on?

I then loaded the AppV package&#8217;s REGISTRY.DAT file and browsed to the registry key that Procmon was saying was generating the error and sure enough, the key was missing. &nbsp;I then went back to the sequencer that still had the install and looked at the &#8216;Virtual Registry&#8217; tab and compared it to what actually existed in the registry.

<div style="clear: both; text-align: center;">
  <a href="https://3.bp.blogspot.com/-zbXs5GIhhuQ/Vs4v6gO0eSI/AAAAAAAABk0/KtycPvPReJc/s1600/PastedGraphic-2.tiff" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="462" src="http://theorypc.ca/wp-content/uploads/2016/02/PastedGraphic-2-1-300x217.jpg" width="640" /></a>
</div>

<img alt="Inline image 1" class="Apple-web-attachment-container" src="cid:ii_153044beaf566a52" style="font-family: Helvetica; font-size: 12px; margin-right: 25px;" /> 

And the keys are missing in the sequence but exist in the registry. &nbsp;So&#8230; &nbsp;Why was this not captured?

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.45.08-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.45.08-2BPM-1-257x300.png" width="273" /></a>
</div>

The sequencer is supposed to work by taking a &#8216;snapshot&#8217; of the system, you make your changes, and it records the differences.

Where does it record those differences?

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.46.41-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="184" src="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.46.41-2BPM-1-300x173.png" width="320" /></a>
</div>

%temp%scratch

The files that it generates to record these differences are the &#8216;ingredients&#8217; files, among the others. &nbsp;These files are actually XML text files. &nbsp;Looking into these files we can see if our keys were captured.

<div style="clear: both; text-align: center;">
  <a href="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.52.09-2BPM-1.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="179" src="http://theorypc.ca/wp-content/uploads/2016/02/Screen-2BShot-2B2016-02-24-2Bat-2B3.52.09-2BPM-1-300x168.png" width="320" /></a>
</div>

Our keys \*were\* captured. &nbsp;But for some reason they are getting marked as &#8216;KeyDeleted&#8217;. &nbsp;Are there actual delete commands being issued during the sequencing of this application? &nbsp;I know which part would be issuing this command. &nbsp;If I do the install without the client pack then all registry keys are captured appropriately. &nbsp;As soon as I install the client pack I find the products aren&#8217;t completely captured in the package.

My first thought was this issue is my sequencer. &nbsp;I asked for some help and [Dan Gough](http://packageology.com/)&nbsp;tried sequencing this package. &nbsp;He did it and no registry keys were missing. &nbsp;He did this on a Windows 10 box.

I then took this software and tried packaging it on my home lab. &nbsp;I setup numerous operating systems, Windows 7, Server 2008 R2 SP1, Server 2012 R2, and Windows 10. &nbsp;All 4 operating systems sequenced the application without losing any registry keys. &nbsp; So what the heck is happening?

I went back to our production environment and rebuilt the sequencer, but I rebuilt it to the most minimal specifications possible. &nbsp;No Windows Update, not even joined to the domain.

And I&#8217;m still missing keys.

So what do I have here? &nbsp;I have sequenced this application a few times and I can reliably have it &#8216;not capture&#8217; all registry keys in this one, unique environment. &nbsp;In a couple other environments it works without issue. &nbsp;The common failure is our production cluster. &nbsp;I then moved the sequencer VM to another cluster to try and see if it&#8217;s our specific cluster.

And it still occurred. &nbsp;So it may still be the cluster, VMWare configuration (?), or something. &nbsp;At this point, I just don&#8217;t know.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->