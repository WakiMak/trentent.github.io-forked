---
id: 552
title: Citrix Seamless Flags and their impact
date: 2015-07-23T18:03:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/07/23/citrix-seamless-flags-and-their-impact/
permalink: /2015/07/23/citrix-seamless-flags-and-their-impact/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/07/citrix-seamless-flags-and-their-impact.html
blogger_internal:
  - /feeds/7977773/posts/default/556584089228675319
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix

  - XenDesktop
---
While investigating a performance issue with an application in our Citrix farm a curiosity was discovered when someone opened up Process Explorer and found the CPU utilization on the server was much higher than expected or reported by various monitoring tools we use. 

<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-hKAGFlUn_OE/VbFUpYF8edI/AAAAAAAAA_Y/3VO6uAWTnzg/s1600/1.png" style="margin-left: 1em; margin-right: 1em; text-align: center;"><img border="0" height="173" src="http://2.bp.blogspot.com/-hKAGFlUn_OE/VbFUpYF8edI/AAAAAAAAA_Y/3VO6uAWTnzg/s400/1.png" width="400" /></a>
</div>

Examining what was utilizing the CPU we found the process 'winlogon.exe' was consuming nearly the entire difference between Process Explorer and Task Manager.

<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-JelIeMxmckc/VbFVA7gYxXI/AAAAAAAAA_o/yEgC4l4QXSw/s1600/2.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="400" src="http://2.bp.blogspot.com/-JelIeMxmckc/VbFVA7gYxXI/AAAAAAAAA_o/yEgC4l4QXSw/s400/2.png" width="300" /></a>
</div>

<div style="clear: both; text-align: center;">
</div>

<div style="font-family: Helvetica; font-size: 12px; min-height: 14px;">
</div>

<div style="min-height: 14px;">
  Process Explorer allows us to dive 'deeper' into the process to determine the thread that is utilizing our CPU.
</div>

<div style="clear: both; text-align: center;">
  <a href="http://3.bp.blogspot.com/-lnOJM9ZPvKY/VbFVxC2rKxI/AAAAAAAABAA/BfSw0fcio6A/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B2.58.15%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://3.bp.blogspot.com/-lnOJM9ZPvKY/VbFVxC2rKxI/AAAAAAAABAA/BfSw0fcio6A/s320/Screen%2BShot%2B2015-07-23%2Bat%2B2.58.15%2BPM.png" width="256" /></a>
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  twi3.dll is a Citrix file. &nbsp;Click 'Module' and getting properties gives us more information about the file and its purpose.
</div>

<div style="min-height: 14px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-34UKvUdmZFs/VbFXV5NivjI/AAAAAAAABAQ/C4v2pnXvPyY/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B3.05.10%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://1.bp.blogspot.com/-34UKvUdmZFs/VbFXV5NivjI/AAAAAAAABAQ/C4v2pnXvPyY/s320/Screen%2BShot%2B2015-07-23%2Bat%2B3.05.10%2BPM.png" width="259" /></a>
</div>

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-GkeXY71mMyc/VbFXVw8T4PI/AAAAAAAABAM/7f2z-o02E0E/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B3.05.04%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="320" src="http://1.bp.blogspot.com/-GkeXY71mMyc/VbFXVw8T4PI/AAAAAAAABAM/7f2z-o02E0E/s320/Screen%2BShot%2B2015-07-23%2Bat%2B3.05.04%2BPM.png" width="262" /></a>
</div>



<div style="clear: both; text-align: center;">
</div>

<div style="text-align: center;">
  "Seamless 2.0 Host Agent - main component"
</div>

<div style="min-height: 14px;">
  <span style="background-color: white; color: #545454; font-family: arial, sans-serif; font-size: x-small; line-height: 18px;"><br /></span>
</div>

<div style="min-height: 14px;">
  <span style="background-color: white; color: #545454; font-family: arial, sans-serif; font-size: x-small; line-height: 18px;"><br /></span>
</div>

<div style="min-height: 14px;">
  &nbsp;Now that we have an idea of the purpose of twi3.dll is, we can being to test why it's consuming so much CPU. &nbsp;Citrix has options for modifying the behaviour of twi3.dll via <a href="http://support.citrix.com/article/CTX101644">"Seamless Flags"</a>.
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  For our environment we had the following set:
</div>

<div style="min-height: 14px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-I0NL2hDIHg8/VbFePV4aQyI/AAAAAAAABAk/aMM1nN5rzi0/s1600/SeamlessFlags_Settings.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="302" src="http://1.bp.blogspot.com/-I0NL2hDIHg8/VbFePV4aQyI/AAAAAAAABAk/aMM1nN5rzi0/s320/SeamlessFlags_Settings.png" width="320" /></a>
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  I experimented with modifying each value to determine what the impact on the CPU on the twi3.dll threads would experience.
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  To that end, you can modify these values immediately and they take effect on the next session connect (but does not impact existing sessions). &nbsp;With that, here were my results:
</div>

<div style="min-height: 14px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://4.bp.blogspot.com/-HM-W8mk3_CM/VbFgLHWwYSI/AAAAAAAABAw/CgTqe4jnK_c/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B3.43.28%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="198" src="http://4.bp.blogspot.com/-HM-W8mk3_CM/VbFgLHWwYSI/AAAAAAAABAw/CgTqe4jnK_c/s640/Screen%2BShot%2B2015-07-23%2Bat%2B3.43.28%2BPM.png" width="640" /></a>
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  Lower numbers for CPU% are better and per user. &nbsp;More CPU's actually lower the maximum % winlogon.exe can consume, for this test it was done with a 6 core CPU. &nbsp;Less CPU's and the maximum % increases.. &nbsp;I imagine this is due to a thread limit or some such? &nbsp;
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  The values that had the most impact on CPU utilization were the lowest values for the WorkerWaitInterval or WorkerFullCheckInterval followed by Disable Active Accessibility Hook.
</div>

<div style="min-height: 14px;">
</div>

<div style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-UPuZNaNyrds/VbFwgHjVlBI/AAAAAAAABBA/DfMoyMLAqC0/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B4.53.24%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="252" src="http://2.bp.blogspot.com/-UPuZNaNyrds/VbFwgHjVlBI/AAAAAAAABBA/DfMoyMLAqC0/s320/Screen%2BShot%2B2015-07-23%2Bat%2B4.53.24%2BPM.png" width="320" /></a>
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
  So what does WorkerWaitInterval / WorkerFullCheckInterval do?
</div>

<div style="min-height: 14px;">
</div>

<div style="min-height: 14px;">
</div>

<div title="Page 15">
  <div>
    <div>
      <div>
        <span style="font-family: 'Calibri'; font-size: 11.000000pt;">WORKER WAIT INTERVAL / WORKER FULL CHECK INTERVAL</span><br /><span style="font-family: Calibri; font-size: 11pt;">Explanation: This update addresses a custom application's performance when run seamlessly. Some applications appeared to be slower to respond when performing actions such as moving, resizing, or closing windows. This fix introduces two new registry settings that allow administrators to configure an explicit time interval for the seamless engine mechanism to monitor when changes take place in the seamless applications.</span><br /><span style="font-family: 'Calibri'; font-size: 11.000000pt;"><br /></span> <span style="font-family: Calibri; font-size: 11pt;">For both values, a larger size slows responsiveness but improves scalability; a smaller size increases responsiveness but decreases scalability slightly. The level of scalability depends on several factors, such as hardware sizing, types of applications, network performance, and number of users.</span><span style="font-family: 'Calibri'; font-size: 11.000000pt;">&nbsp;</span><br /><span style="font-family: 'Calibri'; font-size: 11.000000pt;"><br /></span>HKEY_LOCAL_MACHINESYSTEMCurrentControlSetControlCitrixwfshellTWI Value Name: WorkerWaitInterval<br />Type: REG_DWORD<br />Value: 0<br />Value: <number allowed="" milliseconds="" of=""> (Values are between 5 - 500; the default is 50.)</number></p> 
        
        <p>
          HKEY_LOCAL_MACHINESYSTEMCurrentControlSetControlCitrixwfshellTWI Value Name: WorkerFullCheckInterval<br />Type: REG_DWORD<br />Value: <number allowed="" milliseconds="" of=""> (Values are between 50 - 5000; the default is 500.)</number></div> </div> </div> </div> 
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
            For the graph above, the WorkerWaitInterval and WorkerFullCheckInterval defined in milliseconds, when they are a low value, forces the seamless engine to monitor for changes at a higher pace. &nbsp;This consumes additional CPU cycles. &nbsp;
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
            For our environment we encountered issues as user count increased. &nbsp;It turns out, as each user logged on they consumed a fairly constant amount of CPU. &nbsp;The winlogon.exe process for the server in the screenshot averaged around 0.8% CPU per user, with 35 users that's 28% of the CPU no longer available. &nbsp;So why does Task Manager not display these values? &nbsp;The <a href="https://www.google.ca/url?sa=t&rct=j&q=&esrc=s&source=web&cd=3&cad=rja&uact=8&ved=0CCoQFjACahUKEwiB5ZnruPLGAhUJKogKHQ43CDY&url=http%3A%2F%2Fmedia.ch9.ms%2Fteched%2Fna%2F2011%2Fppt%2FWCL304.pptx&ei=xX2xVcHuPInUoASO7qCwAw&usg=AFQjCNFCNa8W9sfmksOfP5Pr0A39vkzocw&sig2=-eIWfdvo0rBV0M1fktDzsA&bvm=bv.98476267,d.cGU">author of Process Explorer </a>has the answer:
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="clear: both; text-align: center;">
            <a href="http://4.bp.blogspot.com/-aSfy2UN1GWY/VbF-NpNH9CI/AAAAAAAABBQ/e43k6stLZZI/s1600/Screen%2BShot%2B2015-07-23%2Bat%2B5.51.58%2BPM.png" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="223" src="http://4.bp.blogspot.com/-aSfy2UN1GWY/VbF-NpNH9CI/AAAAAAAABBQ/e43k6stLZZI/s400/Screen%2BShot%2B2015-07-23%2Bat%2B5.51.58%2BPM.png" width="400" /></a>
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
            The configured time range we configured were 5ms so the threads executing have a good chance being before or after the timer tick of 15.6ms.
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
            So this poses a question of what *should* the values be? &nbsp;Citrix Adaptive Display technology has a maximum frame rate of 30 for XenApp 6.5 and a maximum of 60 for XenApp 7+. &nbsp;Potentially, I think to achieve these frame rates a value of 16 for XA7 or 33 for XA65 could be set. &nbsp;If the FPS is set to a lower maximum, it would probably make more sense to do the math for that maximum FPS?
          </div>
          
          <div style="min-height: 14px;">
          </div>
          
          <div style="min-height: 14px;">
            Further testing may be needed here.</p> 
            
            <p>
              Lastly, if you do not use Seamless Flags, e.g., if you use Shift F1 to switch to Window'ed Mode then Winlogon.exe will use no CPU for twi3.dll. &nbsp;You also get the same results with RDP, no CPU being used.
            </p>
          </div>
          
          <!-- AddThis Advanced Settings generic via filter on the_content -->
          
          <!-- AddThis Share Buttons generic via filter on the_content -->