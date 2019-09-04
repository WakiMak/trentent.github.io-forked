---
id: 1057
title: AppV 5 registry keys not applying when set in DynamicConfiguration.xml
date: 2016-04-24T22:37:24-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/605-revision-v1/
permalink: /2016/04/24/605-revision-v1/
---
I recently ran into an issue with AppV 5 and it not applying registry keys/values I set in the DynamicConfiguration.xml file. Everything else seemed to be applying without issue in the XML file, scripts I added, shortcuts manipulated, just not registry values. I created a thread at Microsoft&#8217;s support forum here:  
<span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;"><br /> <a href="http://social.technet.microsoft.com/Forums/en-US/251bae44-54d9-4295-a86b-09e2292c37e3/appv-50-sp2-hf4-registry-not-applying-when-set-via-dynamicconfigxml?forum=mdopappv">http://social.technet.microsoft.com/Forums/en-US/251bae44-54d9-4295-a86b-09e2292c37e3/appv-50-sp2-hf4-registry-not-applying-when-set-via-dynamicconfigxml?forum=mdopappv</a></span>

We even had a PFE on site who was unable to determine the cause. Eventually, I did a clean, generic, click-click install of the AppV client on a non-domain system (to exclude GPO&#8217;s). I manually copied the .appv and .xml over and added/mounted/published the package via powershell with the dynamicconfiguration.xml and I saw my missing registry keys! hurray!

I then took the registry values from our non-working system and compared them to the working system. Adding in keys from the non-working system to the working system until it failed I came across the following culprit:  
<span style="color: #e06666;"><br /> </span>

<pre class="lang:reg decode:true ">NoBackgroundRegistryStaging REG_DWORD 0x1</pre>

<span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;"><br /> </span>When that value was set to 0x1 the registry keys from the dynamic xml file would not apply. If it was deleted or set to 0x0 then the keys applied without issue. I had originally added this key from this article:

<span style="font-family: Arial, Helvetica, sans-serif; font-size: x-small;"><a href="http://blogs.technet.com/b/gladiatormsft/archive/2013/09/25/app-v-on-registry-staging-and-how-it-can-affect-vdi-environments.aspx">http://blogs.technet.com/b/gladiatormsft/archive/2013/09/25/app-v-on-registry-staging-and-how-it-can-affect-vdi-environments.aspx</a></span>

But it appears that article didn&#8217;t specify the full impact of adding this key. Steve noticed that CPU utilization was lower when publishing applications with this key set, but it appears that could be because XML processing on the dynamicconfig.xml isn&#8217;t happening. Because of this removal of this great feature to add keys after package creation I would recommend removing the NoBackgroundRegistryStaging key.

[<img src="https://images-blogger-opensocial.googleusercontent.com/gadgets/proxy?url=http%3A%2F%2F1.bp.blogspot.com%2F-bDnihlGOsmk%2FU6RlvgPHs-I%2FAAAAAAAAAdI%2FeL24W5YptiE%2Fs1600%2Freg-applied.PNG&container=blogger&gadget=a&rewriteMime=image%2F*" border="0" />](http://1.bp.blogspot.com/-bDnihlGOsmk/U6RlvgPHs-I/AAAAAAAAAdI/eL24W5YptiE/s1600/reg-applied.PNG)

[<img src="https://images-blogger-opensocial.googleusercontent.com/gadgets/proxy?url=http%3A%2F%2F3.bp.blogspot.com%2F-SvlqAWlz02I%2FU6Rlv21fmhI%2FAAAAAAAAAdM%2FJJaUimWrofs%2Fs1600%2Freg-not-applied.PNG&container=blogger&gadget=a&rewriteMime=image%2F*" border="0" />](http://3.bp.blogspot.com/-SvlqAWlz02I/U6Rlv21fmhI/AAAAAAAAAdM/JJaUimWrofs/s1600/reg-not-applied.PNG)

Notice the Foo and EmptyKey. Those keys are set via the dynamic.xml fle.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->