---
id: 2203
title: 'Citrix Storefront &#8211; Workspace Control requires a minimum Citrix Receiver version'
date: 2017-05-02T14:44:59-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2203
permalink: /?p=2203
categories:
  - Blog
---
Testing the migration of Citrix Web Interface 5.4 to Storefront 3.9, one of the features we wanted was called &#8220;Workspace Control&#8221;. Â You&#8217;d think it would be easy enough to set this, as this setting exists and works in both products.

However, I was doing some testing today and found that Workspace Control was NOT working on Storefront 3.9, no matter what settings I had enabled.

Here is my Workspace Control setting:

<img class="aligncenter size-full wp-image-2204" src="http://theorypc.ca/wp-content/uploads/2017/05/WorkspaceControl.png" alt="" width="809" height="598" srcset="http://theorypc.ca/wp-content/uploads/2017/05/WorkspaceControl.png 809w, http://theorypc.ca/wp-content/uploads/2017/05/WorkspaceControl-300x222.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/WorkspaceControl-768x568.png 768w" sizes="(max-width: 809px) 100vw, 809px" /> 

See how it says &#8216;Enable workspace control&#8217; is checked? Â That should be it, right? Â And it should just work? Â Notice the two options for enabling &#8216;Show reconnect button&#8217; and &#8216;Show disconnect button&#8217;? Â Those two options imply it modifies the GUI somewhere.

But what was I seeing?

<img class="aligncenter size-full wp-image-2205" src="http://theorypc.ca/wp-content/uploads/2017/05/Activate_About.png" alt="" width="298" height="243" /> 

Just Activate&#8230;, About, and Log Off. Â No &#8220;Reconnect/disconnect/etc.&#8221; Â I would log off this website and log back on but to no avail. Â My sessions weren&#8217;t being moved.

As a test, I enabled HTML5 Receiver.

<img class="aligncenter size-full wp-image-2206" src="http://theorypc.ca/wp-content/uploads/2017/05/Use_HTML5_Recevier.png" alt="" width="778" height="334" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Use_HTML5_Recevier.png 778w, http://theorypc.ca/wp-content/uploads/2017/05/Use_HTML5_Recevier-300x129.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Use_HTML5_Recevier-768x330.png 768w" sizes="(max-width: 778px) 100vw, 778px" /> 

&nbsp;

And logged out and back into the store. Â What did I see now?

<img class="aligncenter size-full wp-image-2207" src="http://theorypc.ca/wp-content/uploads/2017/05/HTML5_Receiver_Menu.png" alt="" width="195" height="293" /> 

Look at that. Â I have some new buttons, &#8220;Connect&#8221; and &#8220;Disconnect&#8221;. Â In addition to those buttons popping up, the HTML5 receiver opened a bunch of browser tabs with my applications. Â So, workspace control is now working.

I then set my deployment option to &#8220;Install locally&#8221;

<img class="aligncenter size-full wp-image-2208" src="http://theorypc.ca/wp-content/uploads/2017/05/Install_Locally.png" alt="" width="787" height="330" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Install_Locally.png 787w, http://theorypc.ca/wp-content/uploads/2017/05/Install_Locally-300x126.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Install_Locally-768x322.png 768w" sizes="(max-width: 787px) 100vw, 787px" /> 

And went back to the Store. Â And I went back down to the three options, and my sessions did <span style="text-decoration: underline;">not</span> automatically open.

On a lark I tried a different machine and lo and behold I got different options:

<img class="aligncenter size-full wp-image-2209" src="http://theorypc.ca/wp-content/uploads/2017/05/4.6Recevier.png" alt="" width="243" height="346" srcset="http://theorypc.ca/wp-content/uploads/2017/05/4.6Recevier.png 243w, http://theorypc.ca/wp-content/uploads/2017/05/4.6Recevier-211x300.png 211w" sizes="(max-width: 243px) 100vw, 243px" /> 

&nbsp;

&#8220;Connect&#8221; and &#8220;Disconnect&#8221; came back. Â And when I went to the URL my sessions popped up, showing that workspace control was working.

So what was happening?

The machine I was originally testing this on had Citrix Receiver 3.4 installed. Â StorefrontÂ **_was_** detecting that Citrix Receiver was installed so it did NOT prompt to install, but it appears Citrix Receiver 3.4 is too old to support Workspace Control in Storefront. Â Applications can still be launched manually but the connect/disconnect and auto-reconnect does NOT work. Â The minimum version required for Workspace Control to work with Storefront 3.9 needs to be Citrix ReceiverÂ 14.0.1.4. Â This version of Receiver works on XP (if you still have XP!) and I believe the [last version to work on XP is 4.1](https://www.citrix.com/downloads/xendesktop/receivers/receiver-for-windows-41.html). Â This version of Receiver does work with Workspace Control but if you use Receiver to create desktop icons I believe you will lose that feature as it existed in 3.4 but didn&#8217;t come back until 4.2 (hence why we&#8217;re still on 3.4)&#8230;

Anyways, if you find Storefront Workspace Control is not working in your environment there is a minimum Citrix Receiver version in order for it to work. Â I didn&#8217;t find any documentation on Citrix&#8217;s website to confirm this outside of my own testing, and confusingly, &#8220;Web Interface Workspace Control&#8221; works just fine with <span style="text-decoration: underline;"><strong>ALLÂ </strong></span>Receiver versions (3.3Â &#8211; 4.7).

So if you are going to use Storefront as part of a migration

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->