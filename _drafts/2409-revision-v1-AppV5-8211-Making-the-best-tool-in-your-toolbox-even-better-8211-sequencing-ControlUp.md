---
id: 2423
title: 'AppV5 &#8211; Making the best tool in your toolbox even better &#8211; sequencing ControlUp'
date: 2017-06-07T20:07:16-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/06/07/2409-revision-v1/
permalink: /2017/06/07/2409-revision-v1/
---
If you haven&#8217;t heard of [ControlUp I would highly suggest you check them out](https://www.controlup.com/). Â I have no affiliation with them but I use their product at my work and I&#8217;m astounded at how much this one tool has [saved me time](https://theorypc.ca/2017/04/13/controlup-realtime-troubleshooting-example/)Â and I **WILL**Â [talk](https://theorypc.ca/2017/01/30/controlup-script-based-action-sba-action-auditing/) [aboutÂ it](https://theorypc.ca/2016/09/07/controlup-dissecting-logon-times-a-step-further-invalid-home-directory-impact/)Â [so](https://theorypc.ca/2016/08/31/controlup-dissecting-logon-times-a-step-further-printer-loading/)Â [that](https://theorypc.ca/2016/08/26/controlup-list-appv5-recent-events-on-various-servers/) [others](https://theorypc.ca/2016/08/23/using-controlup-to-launch-a-citrix-application-published-on-a-server/) [can](https://theorypc.ca/2016/08/13/using-vmware-remote-console-with-controlup/) take back their weekends too.

Ok, so, this tool sounds cool but&#8230; Â how can you make it better?

One of the greatest, most powerful aspects of ControlUp is that each item in the GUI can be turned into a variable and set in aÂ **script based action (SBA).** Â ControlUp has a filtering mechanism that allows you to search for nearly anyÂ string text within the display and filter down to the exact item(s) that you want.

<img class="aligncenter wp-image-2410 size-full" src="http://theorypc.ca/wp-content/uploads/2017/06/MainGUI.png" alt="" width="3241" height="1975" srcset="http://theorypc.ca/wp-content/uploads/2017/06/MainGUI.png 3241w, http://theorypc.ca/wp-content/uploads/2017/06/MainGUI-300x183.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/MainGUI-768x468.png 768w, http://theorypc.ca/wp-content/uploads/2017/06/MainGUI-1600x975.png 1600w" sizes="(max-width: 3241px) 100vw, 3241px" /> 

For example. Â We have &#8216;XenApp Worker Group&#8217; and we set a reboot schedule dictated by even and odd servers, so we add oddÂ servers to the &#8216;Reboot-Group1-Odd&#8217; and even numbered servers to &#8216;Reboot-Group2-Even&#8217;. Â In this freaking GUI, we can literally search for &#8220;Even&#8221; or &#8220;Odd&#8221; right here:

<img class="aligncenter size-full wp-image-2411" src="http://theorypc.ca/wp-content/uploads/2017/06/search_filed.png" alt="" width="697" height="372" srcset="http://theorypc.ca/wp-content/uploads/2017/06/search_filed.png 697w, http://theorypc.ca/wp-content/uploads/2017/06/search_filed-300x160.png 300w" sizes="(max-width: 697px) 100vw, 697px" /> 

&nbsp;

And look at this magnificent image:

<img class="aligncenter wp-image-2412 size-full" src="http://theorypc.ca/wp-content/uploads/2017/06/ControlUp_Even.png" alt="" width="3232" height="1978" srcset="http://theorypc.ca/wp-content/uploads/2017/06/ControlUp_Even.png 3232w, http://theorypc.ca/wp-content/uploads/2017/06/ControlUp_Even-300x184.png 300w, http://theorypc.ca/wp-content/uploads/2017/06/ControlUp_Even-768x470.png 768w, http://theorypc.ca/wp-content/uploads/2017/06/ControlUp_Even-1600x979.png 1600w" sizes="(max-width: 3232px) 100vw, 3232px" /> 

See the computers on the left!? Â They are all freaking EVEN numbered servers. Â You can search for systems by vDisk file name if you have some wayward serverÂ that hasn&#8217;t rebooted and is on an old vDisk,

You can search for &#8216;AllowLogons&#8217; or &#8216;Prohibit&#8217; to find all systems with Logons enabled or disabled. Â And each column is sortable. Â And sortable in a way that makes sense! Â Ascending or Decending alphabetically!

Really, just the search and sort are two killer features because they areÂ **_fast_**. Â Everything is instantaneous. Â Amazing. Â I could just go on and on&#8230; Â But I&#8217;m digressing.

Back toÂ AppV + ControlUp&#8230;

Ok, so let&#8217;s get back on track. Â Script based actions allow you to execute a command against a system:

<img class="aligncenter size-full wp-image-2413" src="http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers.png" alt="" width="570" height="574" srcset="http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers.png 570w, http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers-298x300.png 298w, http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2017/06/SBA_Computers-100x100.png 100w" sizes="(max-width: 570px) 100vw, 570px" /> 

See the bottom two actions? Â **&#8220;VMWare: Enable Hot-Add Memory&#8221;** and **&#8220;VMWare: Open VMWare Console&#8221;**

These actions require a couple things installed.

  1. [VMWare PowerCLI](https://my.vmware.com/web/vmware/details?downloadGroup=PCLI650R1&productId=614)
  2. [VMWare Remote Console](https://my.vmware.com/web/vmware/details?downloadGroup=VMRC10&productId=561#product_downloads)

These must be installed alongside ControlUp&#8230; Â Or do they? Â<img class="alignnone wp-image-2414" src="http://theorypc.ca/wp-content/uploads/2017/06/smirk.png" alt="" width="32" height="32" srcset="http://theorypc.ca/wp-content/uploads/2017/06/smirk.png 225w, http://theorypc.ca/wp-content/uploads/2017/06/smirk-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2017/06/smirk-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2017/06/smirk-100x100.png 100w" sizes="(max-width: 32px) 100vw, 32px" /> 

With AppV we can create a sequence for ControlUp that automatically includes these programs with the AppV package. Â This means that, as an organization, wherever you deliver the AppV package the user will get all the perquisites needed to execute all the SBA&#8217;s without worrying if the user has them installed, are they outdated, etc. Â They live and die with the AppV package.

And there is a second problem posed by ControlUp that is solved by AppV. Â If you ask your users to install ControlUp via the website or a local server download you have probably encountered the dreaded &#8220;there is nothing here&#8221; display. Â Users open ControlUp to a blank screen asking them to &#8220;Add Computers&#8221;. Â This occurs because [ControlUp saves its configuration to an XML file that is unique per-user](https://support.controlup.com/hc/en-us/articles/206536069-When-working-in-Standalone-Offline-mode-how-can-I-share-my-existing-ControlUp-configuration-with-other-admins-in-the-same-organization-). Â With AppV we can take a completed and configured XML configuration file, add it to our package in a &#8220;PER-USER&#8221; context. Â What this means is we can deploy our AppV package to whomever in our organization and they can make customizations to it that will be stored in their %userprofile%\Roaming\AppV profile and that will roam with them! Â So their changes will be persistent!

Literally, the best of both worlds. Â With AppV you can deliver a pre-configured ControlUp complete with SBA dependencies, and the ability for the user to customize on the fly for the user&#8217;s wishses. Â And with AppV, installing and removing is EXTREMELY simple. Â Everything comes and everything goes simply and easily.

So what is the sequencing steps?

As with 99% of apps that I sequence, I have a script.

&nbsp;

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->