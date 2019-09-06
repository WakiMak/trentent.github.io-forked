---
id: 548
title: PNP_DETECTED_FATAL_ERROR
date: 2015-09-11T15:52:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/11/pnp_detected_fatal_error/
permalink: /2015/09/11/pnp_detected_fatal_error/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/pnpdetectedfatalerror.html
blogger_internal:
  - /feeds/7977773/posts/default/3058965293905812970
categories:
  - Blog
  - Uncategorized
tags:
  - BSOD
  - Citrix
  - Citrix Receiver
  - WinDBG
---
I rebooted my computer to this lovely Blue Screen Of Death (BSOD) message:

<div>
</div>

<div>
  PNP_DETECTED_FATAL_ERROR
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-A-_Te5TZlf0/VfNEsHpCiaI/AAAAAAAABGg/QKa7x9e2gUQ/s1600/IMG_7978.JPG"><img src="http://2.bp.blogspot.com/-A-_Te5TZlf0/VfNEsHpCiaI/AAAAAAAABGg/QKa7x9e2gUQ/s640/IMG_7978.JPG" width="640" height="480" border="0" /></a>
</div>

<div>
</div>

<div>
  Attempting to reboot into Safe Mode also resulted in the same message. Â I was able to boot into 'Recovery Mode' which is a 'Windows PE' mode that runs a stripped down version of Windows in RAM. Â From here I enabled the network 'Kernel Debugging' by <a href="https://msdn.microsoft.com/en-us/library/windows/hardware/hh439346(v=vs.85).aspx">configuring some parameters in the BCD file</a>.
</div>

<div>
</div>

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-Mx90o8S3Hx4/VfNFNVdw29I/AAAAAAAABGo/ENxCuYqMw80/s1600/IMG_7976.JPG"><img src="http://3.bp.blogspot.com/-Mx90o8S3Hx4/VfNFNVdw29I/AAAAAAAABGo/ENxCuYqMw80/s640/IMG_7976.JPG" width="640" height="480" border="0" /></a>
</div>

<div>
</div>

<div>
  The two parameters I set where:
</div>

<div>
</div>

> **bcdedit /store C:\boot\bcd /debug on  
> bcdedit /store C:\boot\bcd /dbgsettings net hostip:192.168.1.101 port:49152**

<div>
  <p>
    <strong><i><br /> </i></strong>I needed to set the "/store" parameter to ensure I was manipulating my non-booting BCD file, and not the BCD file that Windows Recovery boots from. Â Write down the key or save it someplace, you'll need it on the 'host' computer (see in the above screenshot).</div> 
    
    <div>
    </div>
    
    <div>
      Once here I downloaded and installed '<a href="https://msdn.microsoft.com/en-us/windows/hardware/hh852365.aspx">WinDBG.exe</a>'. Â Open windbg.exe and choose "<b>File > Kernel Debug</b>". Â On the 'NET' tab, enter your 'Port' number and 'Key' (everything to right of the equal sign) and click 'OK'.
    </div>
    
    <div>
    </div>
    
    <div style="clear: both; text-align: center;">
      <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-thk834clNng/VfNHvh00gVI/AAAAAAAABG0/3tokA0vKh6Y/s1600/KDebug_Net.PNG"><img src="http://2.bp.blogspot.com/-thk834clNng/VfNHvh00gVI/AAAAAAAABG0/3tokA0vKh6Y/s320/KDebug_Net.PNG" width="320" height="235" border="0" /></a>
    </div>
    
    <div>
    </div>
    
    <div>
      Even though I 'enabled' debug in my BCD file, I found I still needed to tap the 'F8' key while booting and select 'Debugging Mode'. Â Once selected, my windbg.exe on my host computer sprang to life!
    </div>
    
    <div>
    </div>
    
    <div>
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-uF7SvKIYLLo/VfNIwF-2pWI/AAAAAAAABG8/WML1s1jsfnQ/s1600/Screen%2BShot%2B2015-09-10%2Bat%2B3.49.33%2BPM.png"><img src="http://4.bp.blogspot.com/-uF7SvKIYLLo/VfNIwF-2pWI/AAAAAAAABG8/WML1s1jsfnQ/s640/Screen%2BShot%2B2015-09-10%2Bat%2B3.49.33%2BPM.png" width="640" height="514" border="0" /></a>
      </div>
      
      <p>
        It turns out you need to enable symbols or else you get an incomplete picture. Â After enabling symbols and running !analyze -v I got the following:
      </p>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://3.bp.blogspot.com/-2ssWj8cb-dU/VfNJbad7O6I/AAAAAAAABHE/wRDp_O8YivY/s1600/Screen%2BShot%2B2015-09-10%2Bat%2B3.57.38%2BPM.png"><img src="http://3.bp.blogspot.com/-2ssWj8cb-dU/VfNJbad7O6I/AAAAAAAABHE/wRDp_O8YivY/s640/Screen%2BShot%2B2015-09-10%2Bat%2B3.57.38%2BPM.png" width="640" height="556" border="0" /></a>
      </div>
      
      <p>
        ctxusbm. Â This is a Citrix driver for their Receiver client that passes through USB to a Citrix session. I had updated Receiver to 14.3.0.5014 last month and I probably hadn't rebooted my computer until Windows Update made me. Â So that's probably why I'm experiencing this issue now. Â To fix this issue, I rebooted into the Windows Recovery mode and deleted all instances of 'ctxusbm' from the SYSTEM hive. Â Specifically, I deleted these locations:<br /> HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{CF2A3345-050B-41D0-BAF5-CD558EFAAE3B}<br /> HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\Root\LEGACY_CTXUSBM<br /> HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\ctxusbm
      </p>
      
      <p>
        Upon the next reboot, my computer came back cleanly and operates without any issues. Â I am going to keep this module removed until the next version of Receiver is released, hopefully, I won't have any more issues. Â Issues with ctxusbm seem relatively prevalent with Citrix.
      </p>
      
      <div style="clear: both; text-align: center;">
        <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-3N5aUK2kTus/VfNLOavwrlI/AAAAAAAABHQ/aeb7l_QniC8/s1600/Screen%2BShot%2B2015-09-11%2Bat%2B3.43.34%2BPM.png"><img src="http://2.bp.blogspot.com/-3N5aUK2kTus/VfNLOavwrlI/AAAAAAAABHQ/aeb7l_QniC8/s1600/Screen%2BShot%2B2015-09-11%2Bat%2B3.43.34%2BPM.png" border="0" /></a>
      </div>
      
      <div style="clear: both; text-align: center;">
      </div>
      
      <div style="clear: both; text-align: center;">
      </div>
    </div>
    
    <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->