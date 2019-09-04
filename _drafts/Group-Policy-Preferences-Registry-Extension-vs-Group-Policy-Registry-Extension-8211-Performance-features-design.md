---
id: 2741
title: 'Group Policy Preferences Registry Extension vs Group Policy Registry Extension &#8211; Performance, features, design'
date: 2018-04-11T23:00:13-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2741
permalink: /?p=2741
categories:
  - Blog
---
I&#8217;ve created this post because I&#8217;ve found the utility of Group Policy Preferences (GPP) Registry Extension to be misunderstood. Â I&#8217;ve read several comments in various forums and discussions saying they perform poorly or advice to avoid using them and use the &#8220;traditional&#8221;, Group Policy Registry Extension. Â I&#8217;m curious to what extent these comments are true or false.

_Note: I&#8217;m focusing specifically on GPP Registry vs the traditional registry policy because GPP Registry can enable different, and possibly more efficient designs by removing limitations._

To start, I&#8217;ve created a traditional group policy object (GPO) with 1000 registry values. Â This object will thoroughly test the performance of the traditional GPO method as it contains an immense number of values. Â For GPP Registry, I&#8217;m going to create a GPO and use the GPP Registry Client Side Extension (CSE) with 1000 registry values. Â I&#8217;ll apply both GPO&#8217;s and use the event viewer to measure the difference in performance. Â The operation I&#8217;m going to use for the GPP Registry CSE is &#8220;Update&#8221; for all 1000 values. Â I&#8217;ve found this operation to be best performing operation.

The Results:

Traditional Method: 1922ms  
GPP: 77157ms

Holy Moly! Â GPP performance is \*terrible\*! Â Why is it so bad? Â I went to process monitor (procmon) to examine the registry application. Â Procmon adds some delay to each operation as it captures them, but it&#8217;s still pretty fast and allows us to get a sense for this performance.

<img class="aligncenter size-full wp-image-2742" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.33.37-PM.png" alt="" width="700" height="81" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.33.37-PM.png 700w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.33.37-PM-300x35.png 300w" sizes="(max-width: 700px) 100vw, 700px" /> 

This is the first registry entry applied via GPP. Â &#8220;TrententTestPreference&#8221;.

<img class="aligncenter size-full wp-image-2743" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.34.45-PM.png" alt="" width="700" height="45" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.34.45-PM.png 700w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-11-at-10.34.45-PM-300x19.png 300w" sizes="(max-width: 700px) 100vw, 700px" /> 

Well&#8230; My math shows that Registry application took 3,802ms. Â So why the heck is it reporting 77,157ms?!

The answer&#8230; Â [Is Resultant Set Of Policies (RSOP)](https://support.microsoft.com/en-ca/help/2020286/unexpectedly-slow-logon-caused-by-large-wmi-repository-in-windows-or-w). Â Because each individual item within a Group Policy Preference can be filtered by a variety of criteria, RSOP must be evaluated for each item! Â Thankfully, this can be disabled and enabled on the fly. Â The registry key to disable RSOP Logging:

HKLM\Software\Policies\Microsoft\Windows\System /v RSoPLogging /d 0x0 /t REG_DWORD

What&#8217;s the performance metric now?

4,219ms. Â Much faster. Â But about 2x slower than the traditional method.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->