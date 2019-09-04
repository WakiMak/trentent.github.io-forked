---
id: 2350
title: 'Citrix Storefront - Performance and Tuning - Part 2 - PNA Traffic simulation'
date: 2017-05-30T10:27:01-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2350
permalink: /2017/05/30/citrix-storefront-performance-and-tuning-part-2-pna-traffic-simulation/
image: /wp-content/uploads/2017/05/icon2.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - PowerShell
  - scripting
  - Storefront
  - Web Interface
---
[In part 1](https://theorypc.ca/2017/05/24/citrix-storefront-performance-testing-and-tuning-part-1/) I created a script to simulate users going through their web browser, logging in to Storefront, and launching an application.

In this post I'm going to simulate "PNA traffic". Â "PNA" is the icons delivered to your desktop or start menu via Citrix Receiver.

When connecting to Storefront or Web Interface the following can occur:

  1. This is your first connection and you need to download/cache the icons from the server
  2. This is a subsequent connection and you only need to validate you have all the icons
  3. You connect and PNA discovers you are missing an icon or have a new application assigned and proceeds to download any missing/different icons

All connections from PNA to Storefront/Web Interface start with "config.xml" . Â From there, it queries for icons and downloads them. Â This is what each of the 3 scenarios described look like:

&nbsp;

<div id="attachment_2360" style="width: 678px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2360" class="wp-image-2360 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/1stConnection.png" alt="" width="668" height="765" srcset="http://theorypc.ca/wp-content/uploads/2017/05/1stConnection.png 668w, http://theorypc.ca/wp-content/uploads/2017/05/1stConnection-262x300.png 262w" sizes="(max-width: 668px) 100vw, 668px" /></p> 
  
  <p id="caption-attachment-2360" class="wp-caption-text">
    The first time you connect via PNA
  </p>
</div>

&nbsp;

<div id="attachment_2361" style="width: 696px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2361" class="wp-image-2361 size-full" src="http://theorypc.ca/wp-content/uploads/2017/05/Sebsequent_Connection.png" alt="" width="686" height="100" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Sebsequent_Connection.png 686w, http://theorypc.ca/wp-content/uploads/2017/05/Sebsequent_Connection-300x44.png 300w" sizes="(max-width: 686px) 100vw, 686px" /></p> 
  
  <p id="caption-attachment-2361" class="wp-caption-text">
    Subsequent connections after icons have been cached.
  </p>
</div>

&nbsp;

<div id="attachment_2362" style="width: 694px" class="wp-caption aligncenter">
  <img aria-describedby="caption-attachment-2362" class="size-full wp-image-2362" src="http://theorypc.ca/wp-content/uploads/2017/05/2NewApplications.png" alt="" width="684" height="163" srcset="http://theorypc.ca/wp-content/uploads/2017/05/2NewApplications.png 684w, http://theorypc.ca/wp-content/uploads/2017/05/2NewApplications-300x71.png 300w" sizes="(max-width: 684px) 100vw, 684px" /></p> 
  
  <p id="caption-attachment-2362" class="wp-caption-text">
    2 new applications published to the user
  </p>
</div>

&nbsp;

The first connection "config.xml" returns an xml file detailing the properties of the store. Â Show icons on desktop/start menu? What are the URL's for resource enumeration, reconnection, change password, etc.? Â What logon methods are available?

All these questions and more are answered by the config.xml.

The second connection "enum.aspx" with a body size of 175 bytes is a POST request for the PRELAUNCH stage of PNA. Â Are there any applications configured for PRELAUNCH? Â POST request looks like this:

<pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<NFuseProtocol version="4.6">
    <RequestAppData>
        <Scope traverse="onelevel" type="PNFolder">$PRELAUNCH$</Scope>
        <DesiredDetails>permissions</DesiredDetails>
        <DesiredDetails>icon-info</DesiredDetails>
        <DesiredDetails>all</DesiredDetails>
        <ServerType>x</ServerType>
        <ServerType>win32</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>content</ClientType>
        <Credentials>
            <UserName>trententtye</UserName>
            <Password encoding="ctx1">PASSWORDLOL</Password>
            <Domain type="NT">BOTTHEORY.LOCAL</Domain>
        </Credentials>
        <ClientName>Win7</ClientName>
        <ClientAddress>10.10.10.10</ClientAddress>
    </RequestAppData>
</NFuseProtocol></pre>

If you don't have any applications configured for PRELAUNCH you receive a response like this:

<pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.4">
    <ResponseAppData>
    </ResponseAppData>
</NFuseProtocol>
</pre>

The next enum.aspx request is a POST request that asks for all the icons:

<pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<NFuseProtocol version="4.6">
    <RequestAppData>
        <Scope traverse="onelevel" type="PNFolder" />
        <DesiredDetails>permissions</DesiredDetails>
        <DesiredDetails>icon-info</DesiredDetails>
        <DesiredDetails>all</DesiredDetails>
        <ServerType>x</ServerType>
        <ServerType>win32</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>content</ClientType>
        <Credentials>
            <UserName>trententtye</UserName>
            <Password encoding="ctx1">PASSWORDLOL</Password>
            <Domain type="NT">BOTTHEORY.LOCAL</Domain>
        </Credentials>
        <ClientName>Win7</ClientName>
        <ClientAddress>10.10.10.10</ClientAddress>
    </RequestAppData>
</NFuseProtocol></pre>

And the response is a rather large one (1,758,490 bytes in my example) with application properties and details. Â Part of the reason it's so large is the icon data is stored as ASCII. Â Here's a small snippet of just 2 applications:

<pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.4">
    <ResponseAppData>
        <AppDataSet>
            <Scope traverse="onelevel" type="PNFolder"></Scope>
            <AppData type="app">
                <InName>CTXFARM:CTXSERVER01 Desktop</InName>
                <FName>CTXSERVER01 Desktop</FName>
                <Details>
                    <Settings appisdisabled="false" appisdesktop="true">
                        <Folder>Admin\Desktops</Folder>
                        <Description>CTXSERVER01 Desktop</Description>
                        <WinWidth>0</WinWidth>
                        <WinHeight>0</WinHeight>
                        <WinColor>8</WinColor>
                        <WinType>percent</WinType>
                        <WinScale>95</WinScale>
                        <SoundType minimum="false">none</SoundType>
                        <VideoType minimum="false">none</VideoType>
                        <Encryption minimum="false">basic</Encryption>
                        <AppOnDesktop value="false">
                        </AppOnDesktop>
                        <AppInStartmenu value="false"></AppInStartmenu>
                        <PublisherName>CTXFARM</PublisherName>
                        <SSLEnabled>false</SSLEnabled>
                        <RemoteAccessEnabled>false</RemoteAccessEnabled>
                        <HasClientHostedApps>false</HasClientHostedApps>
                    </Settings>
                    <Icon>PIAAABPPPAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPPAAAAAPPPAAAAAPPPAAAAAHPPAAAAADPPAAAAABPPAAAAABPPAAAAAAPPAAAAAAPPAAAAAAPOAAAAAAPOAAAAAAPOAAAAAAPOAAAAABPOAAAAABPPAAAAADPPAAAAAHPPIAAAAPPPMABADPPPOABPPPPPPABPPPPPPIBPPPPPPMBPPPPPPOBPPPPPPPBPPPPPPPJPPPPAAAAAMMMMMMMMMMMMMMMMMMAAAAAAAAAAAAAMMOOOOOOOOOOOOOOOOMAAAAAAAAAAAAMMPMOMMMMMMMMMMMMMOOAAAAAAAAAAAAMPOPMOOOMMMMMMOOOOMOAAAAAAAAAAAAMOOOPMMMMMMMMMMMOOMOAAAAAAAAAAAAMOOOOPMMMMMMMMMMMOOOAAAAAAAAAAAAMMOOMMPMMMMMMMMMMMOOAAAAAAAAAAAAAMOOMOMPMMMMMMMMMMMOAAAAAAAAAAAAAMOMOMOMPMMMMPPPPPPMAAAAAAAAAAAAAMOMMOMOMPMPPLLLLDLPPAAAAAAAAAAAAMOMOMOMOMPMLLDLDLLLPPAAAAAAAAAAAMOMMOMOMPPMLDLDLLLPPPLAAAAAAAAAAMOMOMOMMPPMDLLLLLPPPLLAAAAAAAAAAMOMMOMMPPPMLLLPPPPPLLLLAAAAAAAAAMOOMMOMPPPMLLPDDPPLLLDLAAAAAAAAAMOOMOMMPPPMLPDAADPLLDLLAAAAAAAAMMOOOMOMPPPMLPDADDPLDLDLAAAAAAAAMPOOOOMMPPPMLPPDDPLLLDLLAAAAAAAAMOOOOOOMPPPMPPPPPLLLDLLLAAAAAAAAMOOOOOOOMPPMPPLLLLLDLDLAAAAAAAAAMMOPPPPPMPPMPLLLDLDLDLLAAAAAAAAAAMOOOOOOOMPMLLLDLDLDLLAAAAAAAAAAAMIOOPPPPPPMLLDLDLLLLAAAAAAAAAAAAAAIOOPOPPPAALLLLLLAAAAAAAAAAAAAAAAAIOOPOPPAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPOPAAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPPAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHAAAAAAAAAAAAAAAAA</Icon>
                    <IconInfo>
                        <IconType size="32" bpp="4" format="raw">
                        </IconType>
                        <IconType size="48" bpp="32" format="raw">
                        </IconType>
                        <IconType size="32" bpp="32" format="raw">
                        </IconType>
                        <IconType size="16" bpp="32" format="raw">
                        </IconType>
                    </IconInfo>
                </Details>
                <SeqNo>1365817067</SeqNo>
                <ServerType>win32</ServerType>
                <ClientType>ica30</ClientType>
            </AppData>
            <AppData type="app">
                <InName>CTXFARM:CTXSERVER02 Desktop</InName>
                <FName>CTXSERVER02 Desktop</FName>
                <Details>
                    <Settings appisdisabled="false" appisdesktop="true">
                        <Folder>Admin\Desktops</Folder>
                        <Description>CTXSERVER02 Desktop</Description>
                        <WinWidth>0</WinWidth>
                        <WinHeight>0</WinHeight>
                        <WinColor>8</WinColor>
                        <WinType>percent</WinType>
                        <WinScale>95</WinScale>
                        <SoundType minimum="false">none</SoundType>
                        <VideoType minimum="false">none</VideoType>
                        <Encryption minimum="false">basic</Encryption>
                        <AppOnDesktop value="false">
                        </AppOnDesktop>
                        <AppInStartmenu value="false"></AppInStartmenu>
                        <PublisherName>CTXFARM</PublisherName>
                        <SSLEnabled>false</SSLEnabled>
                        <RemoteAccessEnabled>false</RemoteAccessEnabled>
                        <HasClientHostedApps>false</HasClientHostedApps>
                    </Settings>
                    <Icon>PIAAABPPPAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPOAAAAAPPPAAAAAPPPAAAAAPPPAAAAAHPPAAAAADPPAAAAABPPAAAAABPPAAAAAAPPAAAAAAPPAAAAAAPOAAAAAAPOAAAAAAPOAAAAAAPOAAAAABPOAAAAABPPAAAAADPPAAAAAHPPIAAAAPPPMABADPPPOABPPPPPPABPPPPPPIBPPPPPPMBPPPPPPOBPPPPPPPBPPPPPPPJPPPPAAAAAMMMMMMMMMMMMMMMMMMAAAAAAAAAAAAAMMOOOOOOOOOOOOOOOOMAAAAAAAAAAAAMMPMOMMMMMMMMMMMMMOOAAAAAAAAAAAAMPOPMOOOMMMMMMOOOOMOAAAAAAAAAAAAMOOOPMMMMMMMMMMMOOMOAAAAAAAAAAAAMOOOOPMMMMMMMMMMMOOOAAAAAAAAAAAAMMOOMMPMMMMMMMMMMMOOAAAAAAAAAAAAAMOOMOMPMMMMMMMMMMMOAAAAAAAAAAAAAMOMOMOMPMMMMPPPPPPMAAAAAAAAAAAAAMOMMOMOMPMPPLLLLDLPPAAAAAAAAAAAAMOMOMOMOMPMLLDLDLLLPPAAAAAAAAAAAMOMMOMOMPPMLDLDLLLPPPLAAAAAAAAAAMOMOMOMMPPMDLLLLLPPPLLAAAAAAAAAAMOMMOMMPPPMLLLPPPPPLLLLAAAAAAAAAMOOMMOMPPPMLLPDDPPLLLDLAAAAAAAAAMOOMOMMPPPMLPDAADPLLDLLAAAAAAAAMMOOOMOMPPPMLPDADDPLDLDLAAAAAAAAMPOOOOMMPPPMLPPDDPLLLDLLAAAAAAAAMOOOOOOMPPPMPPPPPLLLDLLLAAAAAAAAMOOOOOOOMPPMPPLLLLLDLDLAAAAAAAAAMMOPPPPPMPPMPLLLDLDLDLLAAAAAAAAAAMOOOOOOOMPMLLLDLDLDLLAAAAAAAAAAAMIOOPPPPPPMLLDLDLLLLAAAAAAAAAAAAAAIOOPOPPPAALLLLLLAAAAAAAAAAAAAAAAAIOOPOPPAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPOPAAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPPAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOOPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIOAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHAAAAAAAAAAAAAAAAA</Icon>
                    <IconInfo>
                        <IconType size="32" bpp="4" format="raw">
                        </IconType>
                        <IconType size="48" bpp="32" format="raw">
                        </IconType>
                        <IconType size="32" bpp="32" format="raw">
                        </IconType>
                        <IconType size="16" bpp="32" format="raw">
                        </IconType>
                    </IconInfo>
                </Details>
                <SeqNo>1365817079</SeqNo>
                <ServerType>win32</ServerType>
                <ClientType>ica30</ClientType>
            </AppData></pre>

The subsequent POST request for icons (most roughly ~21,000 bytes in size) look like this:

<pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<NFuseProtocol version="4.6">
    <RequestAppData>
        <Scope traverse="onelevel" type="PNFolder" />
        <DesiredDetails>icon-info</DesiredDetails>
        <DesiredIconData size="48" bpp="32" format="raw" />
        <DesiredIconData size="32" bpp="32" format="raw" />
        <DesiredIconData size="16" bpp="32" format="raw" />
        <AppName>CTXFARM:Microsoft Word</AppName>
        <ServerType>all</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>rade</ClientType>
        <ClientType>content</ClientType>
        <Credentials>
            <UserName>trententtye</UserName>
            <Password encoding="ctx1">PASSWORDLOL</Password>
            <Domain type="NT">BOTTHEORY.LOCAL</Domain>
        </Credentials>
        <ClientName>Win7</ClientName>
        <ClientAddress>10.10.10.10</ClientAddress>
    </RequestAppData>
</NFuseProtocol></pre>

With the response being:

<pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.4">
    <ResponseAppData>
        <AppDataSet>
            <Scope traverse="onelevel" type="PNFolder"></Scope>
            <AppData type="app">
                <InName>CTXFARM:Microsoft Word</InName>
                <FName>Microsoft Word</FName>
                <Details>
                    <Settings appisdisabled="false" appisdesktop="false">
                        <Description>Microsoft Word</Description>
                        <WinWidth>0</WinWidth>
                        <WinHeight>0</WinHeight>
                        <WinColor>0</WinColor>
                        <WinScale>0</WinScale>
                        <VideoType minimum="false">none</VideoType>
                        <AppOnDesktop value="false">
                        </AppOnDesktop>
                        <AppInStartmenu value="false"></AppInStartmenu>
                        <SSLEnabled>false</SSLEnabled>
                        <RemoteAccessEnabled>false</RemoteAccessEnabled>
                        <HasClientHostedApps>false</HasClientHostedApps>
                    </Settings>
                    <IconInfo>
                        <IconType size="32" bpp="4" format="raw">
                        </IconType>
                        <IconType size="48" bpp="32" format="raw">
                        </IconType>
                        <IconType size="32" bpp="32" format="raw">
                        </IconType>
                        <IconType size="16" bpp="32" format="raw">
                        </IconType>
                    </IconInfo>
                </Details>
                <IconData size="48" bpp="32" format="raw">/4AAAAADAAD+AAAAAAMACPgAAAAAAwAz8AAAAAAD1v/gAAAAAAPu/8AAAAAAA///wAAAAAAD//+AAAAAAAP//4AAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAD//wAAAAAAAP//AAAAAAAA//8AAAAAAAD//wAAAAAAAf//AAAAAAAB//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP//wAAAAAAA///AAAAAAAD//8AAAAAAAP+/wAAAAAAA/v/AAAAAAAD+////////////////////+j/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD2nmIBBq6VEQaeqPEGnqvhBo694QZ+r0D2bq/g9k6v8PY+j/D2Po/w5h6P8PX+j/Dl/n/w5d5f8OXOb/Dlrk/w5Y5P8NV+P/DFbj/w1U4f8MU+H/DFHg/wxP3/8LTt7/C0ze/wtL3f8KSd3/Ckfc/wpF2v8KRNr/CULZ/wlB2P8JP9f/CD3W/wg71f8AAAAOAAAABQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABFq6T0QauuREWrt2XOo9P671Pn/4uz9//T4/v///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////wg51f8AAAArAAAADgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAARaugrEGzscRFs7dKSu/f/4Ov9/+z2/v/Z7f3/y+b7/8Hi+v+73vr/vd/7/7ve+/+73fv/ut38/7nb+/+52vv/t9r7/7fZ+/+32Pr/ttj7/7XX+v+11vr/tNb7/7TW+/+01fv/s9X6/7PV+v+01fv/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VYX2/1aF9v9Yhvb/WYn2/1uL9v9cjPf//////wQhyf8AAAA6AAAAEwAAAAAAAAAAD2Tp//////+63fv/tdn7/7LX+/+w1fr/rtP7/6zR+v+qzvr/rM76/wtd5P8MX+X/DGDm/wxh5v8MYeb/+fv///////////////////////+0yvX/CU3b/wdI2f8GRNb/Bj/U/wQ70v+kte3/+fr+/1GA8/9Qf/b/T3/2/0999v9Pffb/T332/09+9f9Qf/b/UH/2/1KB9v9Tg/b/VYX2/1eH9v9Yifb//////wQgyf8AAAA6AAAAEwAAAAAAAAAAD2Lp//////+63Pv/tNj7/7HW+v+v0/v/rtH6/6vP+v+pzPr/zeL8/wtZ4v8LWuP/DFzj/wtc5P8MW+P/+fv///////////////////////8gXt//B0nZ/wdF1/8GQtX/BT3T/wQ50P/2+P3/ydTz/1Z52v9Qdt7/THbp/0t48v9Kevb/S3r2/0t69v9Mevb/TXv2/0599v9Qf/b/UoH2/1OD9v9Whfb//////wQfyP8AAAA6AAAAEwAAAAAAAAAAD2Hn//////+62/v/stf7/7DU+/+u0vr/rND6/6nN+v+oy/r/7/X+/wpU3/8LVuD/Clfh/wtY4f8KV+D/+fv////////+/v//xdX2/yBc3v8IStn/B0bX/wdC1f8GPtP/BTrR/wk50P/5+v7//////9/l+P+csOr/YoHd/0dq2P9Ea+H/Q27q/0Vz8/9HdvX/SXf1/0p59f9Levb/Tn32/1B/9v9Sgfb//////wMdx/8AAAA6AAAAEwAAAAAAAAAAD2Dn//////+52/v/stb7/6/T+v+t0fv/q8/6/6nN+v+10vv//////wlQ3P8JUd3/ClLd/wlS3v8KUt7/CVDd/wlP3f8ITtz/CEva/wdJ2f8GRdf/B0LV/wU+1P8FO9L/BDfP/6i47v/5+v7//////////////////////8XQ8/+Inub/SWrY/ztd1v87YuH/P2vs/0V09f9HdvX/Snj2/0x89f9Offb//////wMcxv8AAAA6AAAAEwAAAAAAAAAADl7m//////+32vr/sNX6/6/T+v+t0Pv/rs/6/6HH+v+20vr//////wdK2f8IS9v/CUza/whN2/8ITdv/CEza/wdL2v8HSNn/B0bX/wdE1v8GQdX/Bj/T/wU60v8EOM//O2DY//n6/v///////////////////////////////////////////7TB8P90i+L/MlXU/y9S1v80XOD/QG7v/0l59f9Le/X//////wMbxv8AAAA6AAAAEwAAAAAAAAAADl3l//////+32fv/sNT7/67S+v+x0vr/k8D5/4G0+P/n8P3//////wdF1/8HR9f/B0fY/wdH2f8IR9j/B0bY/wdF1/8HRNb/BkHV/wZA0/8FPdL/BTrR/wQ4z/87YNj/+fr+////////////////////////////////////////////////////////////8vT8/7C97v9Rbtv/J0nT/yhN1v8yW+H/5Oj5/wIZxf8AAAA6AAAAEwAAAAAAAAAADlvm//////+32fv/sdT6/63R+v+Ju/j/g7T4/466+P///////////wZB1P8GQdX/BkLW/wZD1v8GQtX/BkHV/wZA1P8FP9T/BT7T/wQ70v8EOdD/BDbP/6q57v/5+v7//v///8HW//+Mr///bZj//2eR//9+of//tcj///D0////////////////////////////////////////5Oj5/56t6/87W9f/H0HR/xg4zv8AAAA6AAAAEwAAAAAAAAAADlrk//////+42fv/qtD6/4m8+P+Gt/j/grX4/7bS+v///////////wQ70v8FPdL/BT3T/wU90v8FPdP/epbn/2iH4/9ohuP/Z4Xi/3aR5f/Y3vf/+fr+///////r8v//XJX//1WN//9Ohf//R33//z91//84bf//MWX//ypd//9qjP//////////////////////////////////////////////////1Nv2/3+S5P85OTlDAAAAFAAAAAAAAAAADVnk//////+p0fr/i7/4/4i7+P+Et/j/gbL4/+/1/v///////////wQ30P8EOM//BDjQ/wU40P8EOND/+fr+//////////////////////////////////T4//9ln///Xpf//1aP//9Ph///SH///0F3//85b///Mmf//8zY//+Pqf//HE///9/m////////////////////////////////////////////////////////2dnZXQAAAAgAAAACDVfj//////+Pwvn/i774/4e6+P+Dtvj/lsD5/////////////////wMzzf8DM87/AzPN/wMzzv8DNM7/+fr+/////////////////////////////////77Y//9moP//X5j//1eQ//9QiP//SYD//0J4//86cP//bZP/////////////rb///xhK///d5f//////////////////////////////////////////////////b29vRwAAABYAAAAEDVbi//////+Nwfn/ir34/4e4+P+Ctfj/xt37/////////////////wIvy/8DL8v/Ai/L/wIvy/8CL8v/+fr+/////////////////////////////////7XS//9nof//YJn//1mR//9Rif//SoH//0N5//9AdP///////////////////////4mj//9Aaf/////////////////////////////////////////////7+/vSAAAAOwAAABgAAAADDVTi//////+NwPj/ib34/4a5+P+Ctfj/7/X+/////////////////wIryf8CK8n/AivJ/wEsyf8BLMn/+fr+/////////////////////////////////9Xm//9oov//YZr//1qS//9Siv//S4L//0R6//9ijv//rcP//+Pq//////////////f5//8YSv//7vL////////////////////////////////////////T09OGAAAAMQAAABAAAAABDFLh//////+NwPj/ibz4/4W4+P+Ywvn//////////////////////wEnx/8BKMj/ASjH/wApx/8BKMj/+fr+//////////////////////////////////////99r///Ypz//1uU//9UjP//TIT//0V8//8+dP//N2z//y9k//8oXP//Unr//5iu//8YSv//ytb///////////////////////////////////////80NDRJAAAAJAAAAAgAAAAADFHg//////+NwPn/ibz4/4W4+P/I3vz//////////////////////wAlxv8BJcX/ACXG/wEmxv8AJsb/+fr+///////////////////////////////////////q8v//Y53//1yV//9Vjf//ToX//0Z9//8/df//OG3//zFl//8pXf//IlX//xtN//8YSv///////////////////////////////////////7fC8P8AAAA8AAAAGAAAAAMAAAAADE/f//////+Mv/j/ibz4/4W4+P/w9v7//////////////////////wAkxf8AJMX/ACTF/wAkxf8AJMX/+fr+////////////////////////////////////////////9vn//3Kk//9Wjv//T4b//0d+//9Adv//OW7//zJm//8qXv//I1b//xxO//+tvv///////////////////////////////////////3CF4v8AAAA6AAAAFAAAAAAAAAAADE7e//////+Mv/n/iLv4/5vF+f///////////////////////////wAkxf8AJMX/ACTF/wAkxf8AJMX/+fr+//////////////////n6/v///////////////////////f3////////D1///daH//0mA//9BeP//OnD//zNo//8sYP//MmL//7/O/////////////////////////////////////////////yhI0/8AAAA6AAAAEwAAAAAAAAAAC0ze//////+MwPj/ibz4/9Hk/P//////////////////////////////////////////////////////////////////////+Pr+/8TO9//H0vn////////////////////////////2+f/////////////V4v//1eH//9Le///w9P//////////////////////////////////////////////////t8Lw/xY0zf8AAAA6AAAAEwAAAAAAAAAAC0vd//////+MwPn/iLz4//j6/v//////////////////////////////////////////////////////////////////////+Pn+/9jg+f/I0/n/9Pb+/52x+P+zwvr/0Nn8//f5//////////////v9///+/v//////////////////////////////////////////////////////////////////cIXi/wshx/8AAAA6AAAAEwAAAAAAAAAACknc//////+MwPn/rtH6////////////////////////////////////////////////////////////////////////////////////////////9Pb+/7jG+v+Kofn/fJb5/2uJ+v+Yrfz/xtL9////////////////////////////////////////////////////////////////////////////KEjT/wIPwP8AAAA6AAAAEwAAAAAAAAAACkfb//////+NwPj/0+b8///////////////////////////////////////////////////////////////////////19/3/z9b5/+Dm/P/////////////////4+f//zNb9/4Gb+v9UePr/QWj7/zlj/f9ujf7/tMP///////////////////////////////////////////////////////+3wvD/SGPZ/wAMv/8AAAA6AAAAEwAAAAAAAAAACkXb//////+Nwfn/+Pv////////////////////////////////////////////////////////////////////////r7vz/w873/8PO+f/09v7/usn6/9Xd/P/4+f/////////////p7v//qrv+/09z/f8RRP7/DD3+/7TD//////////////////////////////////////////////////9wheL/orDs/wAMv/8AAAA6AAAAEwAAAAAAAAAACkTZ//////+Owfn/ttb7/9ro+/////////////////////////////////////////////////////////////////////////////b4/v/i5/3/lqv4/4if+f95lPn/l6z8/8DN/f/09v/////////////h5///hp7///////////////////////////////////////////////////////8oSNP/8vT8/wAMv/8AAAA6AAAAEwAAAAAAAAAACUPZ//////+Pwvj/i7/4/4i7+P+Lu/j/t9T6/+fv/P//////////////////////////////////////////////////////////////////////+Pr//9Da/P+fsvv/YYH6/1B0+v9Kbvv/eZb9/7bF/////////////////////////////////////////////////////////////7fC8P8iR9f//////wAMv/8AAAA6AAAAEwAAAAAAAAAACkHZ//////+Pw/n/jcD4/4i8+P+FuPj/gbX4/32v9/+Jtfj/utL5/+7z/f//////////////////////////////////////////////////////////////////////9ff//7DA/f9bff3/IU/9/w5A/v8bSf7//////////////////////////////////////////////////////3CF4v8zXuf//////wAMv/8AAAA6AAAAEwAAAAAAAAAACT7X//////+RxPn/jcD5/4q++P+Gufj/g7b4/36x9/97rff/d6n4/3Ok9/+Ksvj/vtP5//b4/v//////////////////////////////////////////////////////////////////////4+n//5Wr//9nhv7/////////////////////////////////////////////////8fP8/x1A0v9FdPX//////wAMv/8AAAA6AAAAEwAAAAAAAAAACT3X/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////wAMv/8AAAA6AAAAEwAAAAAAAAAACDzV/wg61P8IONT/BzbT/wc00/8HMtL/BjHQ/wYw0P8GLs//BizO/wUrzf8FKc3/BCfM/wUmzP8EJcr/BCPK/wQiyv8EIMj/Ax7I/wMdx/8DHMf/AhvG/wIZxf8CGMX/AhbE/wEWxP8CFMP/ARPC/wESwv8BEcH/ARDB/wAPwP8ADsD/AA3A/wANv/8ADL//AAy//wAMv/8ADL//AAy//wAMv/8ADL//AAy//wAMv/8AAAA6AAAAEwAAAAAAAAAAAAAADgAAACsAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAArAAAADgAAAAAAAAAAAAAABQAAAA4AAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAAOAAAABQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA</IconData>
                <IconData size="32" bpp="32" format="raw">8AAAAeAAAAHAAAABgAAAAQAAAAEAAAABAAAAAQAAAAEAAAABAAAAAQAAAAEAAAABAAAAAQAAAAEAAAABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAAAAQAAAAEAAAABAAAAAQAAAAEAAAABAAAAAQAAAAEAAAABAAAAAf////8AAAAAAAAAAAAAAAAAAAAAD2blJBBn6WgPZ+qrEGXq2Q9j6fUPYej1D2Dm/w1d5v8NW+X/DVjj/wxW4v8MU+L/C1Hh/wxP3v8LTN3/Ckjc/wpG2v8KQ9n/CUDY/wk91v8IO9X/CDjT/wY00/8HMtH/Bi7Q/wAAAA4AAAAFAAAAAAAAAAAAAAAAAAAAABBp6VUQaOq9a6Hy/8HY+f/t8/3//P3///////////////////////////////////////////////////////////////////////////////////////////////////////8FLM7/AAAAKwAAAA4AAAAAAAAAAAAAAAAQaelsMX3u5MDX+f/y9/7/7fb+/9ns/P/F4fv/uNj6/7bY+/+11vr/s9T6/7LU+v+x0vv/sND6/6/Q+v+uz/r/rs76/63O+v+tzvr/rc36/63O+v+tzvr/rs76/6/P+v+x0fv//////wUqzf8AAAA6AAAAEwAAAAAAAAAAEGnpVTB97+TX5vz/7/f+/8nk/P+42vv/ttj7/7XW+/+z1fr/sdP7/6/R+v+u0Pr/rc76/6vN+v+qy/r/qcv6/6jK+v+nyfr/p8n6/6fI+v+nyPr/p8n6/6fJ+v+nyfr/qMr6/6zN+v//////BSfM/wAAADoAAAATAAAAABBn5iQQaeq9xNn6/+/3/v+63Pv/DFzk/wxi5v8NZej/DWbo/w1l6P8NYuf/E2Pl/0N95/9lkun/qsLz/9jn/f+mx/r/pcb6/6TF+v+kxfr/o8X6/6PE+v+jxPr/pMX6/6TF+v+lxvr/qcn6//////8EJcv/AAAAOgAAABMAAAAAEGjpaGyj8//0+P7/yuT8/7fa+/8NYuf/Dmfp/w5q6/8ObOv/Dmvr/w1n6f8MYub/C13k/wpW3/8IT9z/MGXd/+zz/v+mxvr/oML6/6DB+v+fwfr/n8D6/6DB+v+gwfr/oMH6/6HC+v+mxfr//////wQjyv8AAAA6AAAAEwAAAAAPaOqrx9z6/+73/v+32vr/ttj6/w1l6P8Oa+v/D2/t/w9w7v8Pb+7/Dmrq/w1l6P8MXuX/Cljh/wlR3f8HSdn/KFra/+3z/v+cvvr/nL36/5y9+v+bvfr/nL36/5y9+v+cvfr/nb/6/6LC+v//////AyHJ/wAAADoAAAATAAAAABBn69nx9f3/2ez8/7bY+/+01fr/stP6/6/R+v8Pcu7/EHXw/w9w7v+TvfT/lbv0/zZ76P8KWeH/CVHd/wdK2f8GQtX/n7Tu/9fk/f+auvr/mrr6/5q5+v+auvr/m7v6/5y8+v+dvfr/ocD6//////8DH8j/AAAAOgAAABMAAAAAD2Xq9f3+///E4Pv/tNf7/7LT+v+w0fr/rs/6/w9v7v8Pce7/D27t/+Tu/P++1Pv/oMD6/0uC6/8JUd3/CEnZ/wZC1f9ig+L/5e3+/4ar+f95ovj/dqD4/2uY+P9tmfj/YZL3/2OT9/9llff//////wMcx/8AAAA6AAAAEwAAAAAPZOn//////7XX+/+z1fv/sdL7/67P+v+10/v/D2vr/w9s6/8Oa+v/1eX7/+nw/f+2zfn/nbn3/whP3P8HR9j/BkHU/0Zt3f/e6P3/W4v3/1qK9v9Zivf/Wor3/1uL9/9cjPf/XY73/1+P9///////AhrG/wAAADoAAAATAAAAAA9j6P//////s9b7/7HT+v+u0Pr/rM36/9Dj/P8NZej/DWXp/w1l6P/U4/r////////////h6vz/CEza/wZF1/8GP9T/THDd/9zm/f9Vhfb/VIX2/1SE9/9VhPb/VoX3/1aG9/9YiPb/Wor3//////8CGcX/AAAAOgAAABMAAAAADmHo//////+y1Pr/r9H6/63O+/+rzPr/6PL+/wxe5P8MX+X/DF/k//n7/v///////////3SY6v8HSNn/BkLW/wQ80v9+l+b/zNf5/0Zw7v9JdfP/T373/09+9v9Qf/b/UoH3/1OC9v9Vhff//////wIWxP8AAAA6AAAAEwAAAAAOX+b//////7HT+/+uz/r/q837/7XS+///////Cljg/wtY4v8KWOH/YI/q/3Oa7P88c+P/CErZ/wdE1v8FPtP/BDjQ/+Xq+f//////w8/4/3aR7/8zWuj/N2Ht/0Ju8v9Me/f/Tn32/1CA9///////ARXD/wAAADoAAAATAAAAAA1d5f//////r9H6/63P+v+qzPr/v9n7//////8IUN3/CVHd/wlR3f8JT9z/CEza/wdI2P8GRNb/Bj/T/wQ60f9+luX///////////////////////L0/f+uvfb/WXbr/yJJ5f8uV+v/RXP0//////8CE8L/AAAAOgAAABMAAAAADVrk//////+w0fr/o8j6/3+x+P/d6/3//////wdJ2f8HSdn/CEnZ/wdI2P8HRdf/BkLV/wU/0/8EOdH/lKjq////////////5Oz//9Xh///h6f//////////////////4uf7/5mp8v8vUOb/NlXm/wQezv8AAAA6AAAAEwAAAAANWOP//////5nE+f+As/j/jbn5////////////BkLV/wZD1f8GQtX/aozl/4Kc6P91kuX/rLzv//v8/v//////xtv//1iQ//9Nhf//Qnn//zht//82af//iqX////////////////////////R2Pn/dInt/2hoaEwAAAAVAAAAAQxW4///////hLf4/3+x+P+41fv///////////8EO9L/BDvR/wU70f////////////////////////////T4//9knv//WpL//0+H//9Ee///OW///42q//+etf//KVn///3+////////////////////////8fHxqAAAABsAAAAEDFXh//////+DtPj/gLH4/+Tv/v///////////wM0zv8ENc7/BDTO////////////////////////////3uz//2ag//9blP//UYj//0Z9//9AdP////////////+Zr///YIP///////////////////////+FhYVaAAAAIgAAAAgMUuH//////4K0+P+WwPr/////////////////Ai7K/wIuy/8CLsr////////////////////////////0+f//aKL//12W//9Siv//SH7//1WE//+Oq///xNP///7+//8YSv//////////////////4OX7/wAAAD8AAAAdAAAABQxQ3///////gbP4/8Da/P////////////////8BKcj/ASrI/wEpx/////////////////////////////////+nyP//X5j//1SM//9JgP//PnX//zRp//8pXf//HVD//xhK//////////////////+To/H/AAAAOwAAABUAAAABC03e//////+Etfj/6/P+/////////////////wAlxf8AJsb/ASXG////////////////////////////+fz///////+uy///VY7//0uC//9Adv//NWv//ytf//8gU///q73//////////////////zZV5v8AAAA6AAAAEwAAAAALS93//////5jC+v/////////////////////////////////////////////////T2ff/ytT5/+br/v/x9P///////93o///q8f//vdH//6G8//9ulf//la///9/m///////////////////g5fv/CCrc/wAAADoAAAATAAAAAAtJ3P//////wdr8//////////////////////////////////////////////////b4/v/w8/7/u8j8/36a/P+Dnf//ssH//+fs////////6/H///z9/////////////////////////////5Oj8f8FH9L/AAAAOgAAABMAAAAACkbb///////p8v7////////////////////////////////////////////j6Pr/w834//n6///x9P7/7vL//7nH//9be///KFT//1d6///Bzv//////////////////////////////////NlXm/wISxf8AAAA6AAAAEwAAAAAJQ9r//////9Dj/P/4+/////////////////////////////////////////f4/v/h5/z/0dr8/4Sd/P+Rp/7/v8z///T2///V3v//dJD//7LB/////////////////////////////+Dl+/8oSOT/AAy//wAAADoAAAATAAAAAAlB2f//////grT4/36v+P+qyvr/1uT9////////////////////////////////////////////7vL//6i7//9ggf//LFn//1p9//+ywf//////////////////////////////////k6Px/3WK7v8ADL//AAAAOgAAABMAAAAACD7X//////+Etvj/frH4/3ms9/91pvj/d6b3/6zH+v/j7P3////////////////////////////////////////////W3///dpL//5Oo//////////////////////////////////82Veb/0tj5/wAMv/8AAAA6AAAAEwAAAAAIPNb/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////AAy//wAAADoAAAATAAAAAAg51f8HN9T/BzPS/wcx0f8GLtD/BSvO/wUozf8EJsz/BCPK/wQgyf8DHsj/AxvG/wMaxf8CF8T/AhXD/wETwv8BEsH/ARDB/wAOwP8ADb//AAy//wAMv/8ADL//AAy//wAMv/8ADL//AAy//wAMv/8ADL//AAAAOgAAABMAAAAAAAAADgAAACsAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAA6AAAAOgAAADoAAAArAAAADgAAAAAAAAAFAAAADgAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAABMAAAATAAAAEwAAAA4AAAAFAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA</IconData>
                <IconData size="16" bpp="32" format="raw">wAD//4AA/v8AAP//AAD4/wAA+v8AAP//AAD//wAA//8AAP//AAD//wAA//8AAP//AAD//wAA/v8AAP//AADb/wAAAAAAAAAAEGnsSRBp7JMQaezUEGns9RBp7P8QZ+v/D2Xq/w5i6f8OX+f/Dlvl/w1X4/8MUuH/C07f/wpJ3f8AAAAAEGnsUkiM8Oq00Pn/7PP+//3+//////////////////////////////////////////////////8JQ9r/EGnsSUiM8Ork7v3/2+j8/7rQ+/+uxf3/rsT//67E//+uxP//rsT//67E//+sw///rsT//67E////////CT7Y/xBp7JO00Pn/2+j8/67E//9CeeL/J2zc/yxy3f8obNz/NXDb/2qS4f/H1PL/8/b//6rA//+rwv///////wg51f8QaezU7PP+/7rQ+/+uxP//I2je/wBZ2v8AW9z/AFjb/wBR1f8ASc//DErK/8fT8f/z9v//pr////////8HM9L/EGns9f3+//+uxf3/rsT//5Gx+P8fb+D/AGHg/3Kl6/+vyfL/F1vV/wBCyP9pi9v//////6G6////////Bi7P/xBo6///////rsT//67E//+sw///IW3h/wBc2/+hwfL//P3//zFs3v8AQMj/VHrV//////+dt////////wUozf8QZur//////67E//+sw///qsD//x9m3f8AU9f/eKPp/8bX9f8kX9T/AD7F/22L2///////l7P///////8EJMv/D2Lp//////+uxP//rMP//6i///8eXtn/AEvR/wNL0v8ARcz/AD/H/w1Bxf/M1vL/7fL//5Ow////////Ax/I/w9e5///////rsT//6vC//+mv///HlbS/wBCyP8EQ8n/FU7K/0dv1P+8ye7/9ff//4ur//+OrP///////wMbxv8OW+X//////67E//+qwP//pr///x1OzP8AOMH/h6Hh/+vv+v+/0P7/k7D//4an//+IqP//i6v///////8CF8X/DFbj//////+uxP//qsD//6a///8bR8j/AC+8/5ap4//9/v//i6v//4Sl//+DpP//hKX//4ur////////AhTD/wxR4P//////rMP//6rA//+mv///JUrG/w41uv+aquL//f3//4mp//+IqP//hKX//4an//+Lq////////wERwv8LTN7//////67E//+rwv//pr///6G6//+dt///l7P//5Ow//+OrP//i6v//4ur//+Jqf//jqz///////8BEcL/Ckfc////////////////////////////////////////////////////////////////////////////ARHC/wlB2f8IPNb/CDfU/wYy0f8GLs//BSnO/wUlzP8DIMr/AxzI/wMZxv8CFsT/ARPC/wERwv8BEcL/ARHC/wERwv8=</IconData>
                <SeqNo>0</SeqNo>
                <ClientType>ica30</ClientType>
            </AppData>
        </AppDataSet>
    </ResponseAppData>
</NFuseProtocol></pre>

Lastly, application launches over PNA are a POST request that look like this:

<img class="aligncenter size-full wp-image-2363" src="http://theorypc.ca/wp-content/uploads/2017/05/PNA.png" alt="" width="676" height="18" srcset="http://theorypc.ca/wp-content/uploads/2017/05/PNA.png 676w, http://theorypc.ca/wp-content/uploads/2017/05/PNA-300x8.png 300w" sizes="(max-width: 676px) 100vw, 676px" /> 

The content of that POST request looks like this:

<pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
<PNAgentResourceRequest>
    <Resource>CTXFARM:Microsoft Word</Resource>
    <Credentials>
        <UserName>trententtye</UserName>
        <Password encoding="ctx1">PASSWORDLOL</Password>
        <Domain type="NT">BOTTHEORY.LOCAL</Domain>
    </Credentials>
    <ClientName>Win7</ClientName>
    <ClientAddress>10.10.10.10</ClientAddress>
    <LogonMethod>sson</LogonMethod>
    <ClientType>ica30</ClientType>
    <ICA_Options>
        <SpecialFolderRedirection>
            <Enabled>false</Enabled>
        </SpecialFolderRedirection>
    </ICA_Options>
</PNAgentResourceRequest></pre>

And the response is a ICA file that Receiver executes.

* * *

With each of these requests we can see that PNA sends your username/password/domain to the server to get the relevant information. Â This means we can script these requests using WCAT as the data being POST'ed is actually static.

Using the [wcat scenario creator](http://fiddler2wcat.codeplex.com/releases/view/35356), download the <a id="fileDownload0" class="FileNameLink" tabindex="9" href="http://fiddler2wcat.codeplex.com/downloads/get/90734">WCATScenarioGenerator.dll</a>Â and save it to: "C:\Program Files (x86)\Fiddler2\Scripts"

<img class="aligncenter size-full wp-image-2364" src="http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT.png" alt="" width="433" height="430" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT.png 433w, http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT-150x150.png 150w, http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT-300x298.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT-50x50.png 50w, http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT-100x100.png 100w" sizes="(max-width: 433px) 100vw, 433px" /> 

&nbsp;

**Open Fiddler**. Â Capture PNA operation by closing Citrix Receiver and reopening it. Â Logon (if necessary) and launch an application. Â If you want a more intensive logon, ensure your icon cache is cleared so that it forces new icons to be downloaded. Â This will be the 'scenario' that we will be executing against. Â Clean up any requests that don't go to your web interface/storefront. Â My FiddlerÂ looks like this:

<img class="aligncenter size-full wp-image-2365" src="http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT_Scenario.png" alt="" width="686" height="243" srcset="http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT_Scenario.png 686w, http://theorypc.ca/wp-content/uploads/2017/05/Fiddler_WCAT_Scenario-300x106.png 300w" sizes="(max-width: 686px) 100vw, 686px" /> 

Select your items and in the black bar enter "wcat addtrans" and hit enter.

<img class="aligncenter size-full wp-image-2366" src="http://theorypc.ca/wp-content/uploads/2017/05/wcat_addtrans.png" alt="" width="680" height="265" srcset="http://theorypc.ca/wp-content/uploads/2017/05/wcat_addtrans.png 680w, http://theorypc.ca/wp-content/uploads/2017/05/wcat_addtrans-300x117.png 300w" sizes="(max-width: 680px) 100vw, 680px" /> 

In the same bar enter "wcat save" and hit enter.

<img class="aligncenter size-full wp-image-2367" src="http://theorypc.ca/wp-content/uploads/2017/05/wcat_save.png" alt="" width="356" height="198" srcset="http://theorypc.ca/wp-content/uploads/2017/05/wcat_save.png 356w, http://theorypc.ca/wp-content/uploads/2017/05/wcat_save-300x167.png 300w" sizes="(max-width: 356px) 100vw, 356px" /> 

&nbsp;

This will create a file called "fiddler.wcat" in your "C:\Program Files (x86)\Fiddler2" folder.

<img class="aligncenter size-full wp-image-2368" src="http://theorypc.ca/wp-content/uploads/2017/05/fiddler.wcat_.png" alt="" width="760" height="507" srcset="http://theorypc.ca/wp-content/uploads/2017/05/fiddler.wcat_.png 760w, http://theorypc.ca/wp-content/uploads/2017/05/fiddler.wcat_-300x200.png 300w" sizes="(max-width: 760px) 100vw, 760px" /> 

This file will actually require some cleanup as the WCAT scenario generator captures things literally and the scenario file requires escaping of double-quotes.

So in the wcat file, lines like this:

<img class="aligncenter size-full wp-image-2369" src="http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File.png" alt="" width="1069" height="162" srcset="http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File.png 1069w, http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File-300x45.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File-768x116.png 768w" sizes="(max-width: 1069px) 100vw, 1069px" /> 

&nbsp;

Need to be modified to look like this:

<img class="aligncenter size-full wp-image-2370" src="http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File_Mod.png" alt="" width="1140" height="167" srcset="http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File_Mod.png 1140w, http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File_Mod-300x44.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/WCAT_File_Mod-768x113.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

&nbsp;

To execute our script, [install wcat](https://www.iis.net/downloads/community/2007/05/wcat-63-x86)Â and launch the controller via the command line like so:

<pre class="lang:batch decode:true">wcctl.exe -t PNA_WithAppLaunch.wcat -f settings.ubr -s CTXWI01Â -p 80Â -c 10 -v 60 -w 300 -u 300 -n 300</pre>

What each of these settings means:

-t = scenario file to use  
-f = settings file to use (just using default)  
-s = server to target  
-p = port to connect  
-c = clients that need to connect (this is the number of wcclient.exe's that will connect to the controller)  
-v = number of virtual clients that will connect  
-w =Â warmup time (clients and virtual clients will connect in the amount they have / warmup time)  
-u = duration for full load (all clients should be connected)  
-n = cooldown, clients start disconnecting (opposite of warmup)

For my example, if I use my parameters for the controller then I need to start 10 clients be executing:

<pre class="lang:batch decode:true ">wcclient.exe Win7.bottheory.local</pre>

(Win7.bottheory.local is running the controller).

This command can be executed 10 times on the same box, even the same one as the controller, or on some random combination of 1-10 different boxes as long as you match the number of "clients" to the controller "client" excepted number of connections.

* * *

Lastly, here is a powershell script to measure the performance of each action of the PNA request. Â You will need to modify it for your domain/(webinterface/storefront URL)/username/password. Â This script will measure the amount of time each stage takes and the total, overall time and saves it as a CSV.

Here's an example of the CSV output:  
<img class="aligncenter size-full wp-image-2371" src="http://theorypc.ca/wp-content/uploads/2017/05/WebInterface_Storefront_CSV.png" alt="" width="1326" height="459" srcset="http://theorypc.ca/wp-content/uploads/2017/05/WebInterface_Storefront_CSV.png 1326w, http://theorypc.ca/wp-content/uploads/2017/05/WebInterface_Storefront_CSV-300x104.png 300w, http://theorypc.ca/wp-content/uploads/2017/05/WebInterface_Storefront_CSV-768x266.png 768w" sizes="(max-width: 1326px) 100vw, 1326px" /> 

Some example commands:

<pre class="lang:batch decode:true ">.\Stress_StoreFrontPNA.ps1 -store http://storefront.bottheory.local/Citrix/Store/PNAgent/ -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -stressComponent "Launch Application"
.\Stress_StoreFrontPNA.ps1 -store http://storefront.bottheory.local/Citrix/Store/PNAgent/ -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL
.\Stress_StoreFrontPNA.ps1 -store http://storefront.bottheory.local/Citrix/Store/PNAgent/ -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -loop $false
.\Stress_StoreFrontPNA.ps1 -store https://webinterface.bottheory.local/Citrix/PNAgent/ -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -stressComponent "Icon-Info All"
.\Stress_StoreFrontPNA.ps1 -store https://webinterface.bottheory.local/Citrix/PNAgent/ -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL</pre>

Examples of "stress component" output:  
<img class="aligncenter size-full wp-image-2372" src="http://theorypc.ca/wp-content/uploads/2017/05/stresscomponent.png" alt="" width="445" height="423" srcset="http://theorypc.ca/wp-content/uploads/2017/05/stresscomponent.png 445w, http://theorypc.ca/wp-content/uploads/2017/05/stresscomponent-300x285.png 300w" sizes="(max-width: 445px) 100vw, 445px" /> 

And the script:

<pre class="lang:ps decode:true"><#
.SYNOPSIS
   This script will test the Citrix PNA site of Storefront or WebInterface.  It will connect, get the configuration
   execute a $prelaunch$ command, get all icons, then query for two individual icons from two randomly chosen applications and
   lastly execute a pull of an ICA file.  This script uses the Encrypt-Ctx1Password function from:
   <a href="https://chentiangemalc.wordpress.com/2016/12/14/encrypt-password-in-ctx1-encoding-with-powershell/">Encrypt Password In CTX1 Encoding With&nbsp;PowerShell</a>
   A file will be output in csv format with the times and duration for how long each stage took to perform it's action.

   This script should be run in a loop to get metrics of how your site is performing while you stress test it.
   Author: Trentent Tye
   Version: 2017.05.23
DESCRIPTION
   A Powershell v5 Script that utilizes invoke-webrequest to connect to a Citrix Storefront server and go through a simulation of the PNA launch process
.PARAMETER store 
   PNA URL -- eg http://storefront2.bottheory.local/Citrix/PNAgent/
.PARAMETER loop 
   Run this script forever or once
.PARAMETER stressComponent 
   Loop the calls to StoreFront for one of the components.  The list of components available to stress:
   "Client Configuration"
   "PRELAUNCH"
   "Icon-Info All"
   "Icon-Individual Request1"
   "Icon-Individual Request2"
   "Launch Application"
.PARAMETER username 
   Username to query the PNA site
.PARAMETER password 
   Password for the user account
.PARAMETER domain 
   Logon domain of the user to query the PNA site.
.EXAMPLE
   .\Stress_StorefrontPNA.ps1 -store "https://webinterface.bottheory.local/Citrix/PNAgent/" -loop $false
   .\Stress_StorefrontPNA.ps1 -store "http://storefront2.bottheory.local/Citrix/Store/PNAgent/" -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -loop $false
   .\Stress_StorefrontPNA.ps1 -store "http://storefront2.bottheory.local/Citrix/Store/PNAgent/" -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -stressComponent "Launch Application"
   .\Stress_StoreFrontPNA.ps1  -store "http://storefront2.bottheory.local/Citrix/Store/PNAgent/" -username trententtye -password c0mplexp@44 -domain BOTTHEORY.LOCAL -stressComponent "Launch Application"

#>
Param
(
    [string]$store,
    [string]$username,
    [string]$password,
    [string]$domain,
    [bool]$loop = $true,
    [string]$stressComponent,
    [switch]$verbose

)

if ($verbose) {
    $VerbosePreference = "continue"
} else {
    $VerbosePreference = "SilentlyContinue"
}


# taken from here:
# https://chentiangemalc.wordpress.com/2016/12/14/encrypt-password-in-ctx1-encoding-with-powershell/
Function Encrypt-Ctx1Password
{
    Param([String]$password)
        [System.Byte[]]$keys  = (0xA5,0x04,0x0F,0x41)
        [System.Byte[]]$pwd = [System.Text.Encoding]::Unicode.GetBytes($password)
        [System.Int32[]]$crypt1 = @()
        [System.Int32[]]$crypt2 = @()
        $crypt1 += ($keys[0] -bxor $pwd[0])
        for ($i = 0; $i -lt $pwd.Count-1;$i++)
        {
            $xor = ($crypt1[$i] -bxor $keys[0])
            $crypt1 += ($xor -bxor $pwd[$i+1])
        }
        $c = 0
        for ($i =0; $i -lt $crypt1.Length;$i++)
        {
            $ch = ($crypt1[$i] -shr $keys[1])
            $ch = ($ch -band $keys[2])
            $ch = ($ch + $keys[3])
            $crypt2 += $ch
 
            $ch = ($crypt1[$i] -band $keys[2])
            $ch = $ch + $keys[3]
            $crypt2 += $ch
        }
        [System.Text.ASCIIEncoding]::ASCII.GetString($crypt2)
}

$ctxpassword = Encrypt-Ctx1Password -password $password


function Generate-RandomIconRequest ([xml]$listOfApps) {
    #we are going to query random apps for their icons and build the XML request.  We have to do this as not all
    #apps have a full suite of icons so we need to ensure we only query icons the app can provide
    $app = $listOfApps.NFuseProtocol.ResponseAppData.AppDataSet.AppData[(Get-Random -Minimum 0 -Maximum ($listOfApps.NFuseProtocol.ResponseAppData.AppDataSet.AppData.Count))]
    

#need to embed DesiredIconData and appName
[xml]$genericPost = @'
<?xml version="1.0" encoding="UTF-8"?>
<NFuseProtocol version="4.6">
    <RequestAppData>
        <Scope traverse="onelevel" type="PNFolder" />
        <DesiredDetails>icon-info</DesiredDetails>
        <ServerType>all</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>rade</ClientType>
        <ClientType>content</ClientType>
        <ClientName>PSStressTest</ClientName>
        <ClientAddress>10.10.10.10</ClientAddress>
    </RequestAppData>
</NFuseProtocol>
'@

    $genericPost = Add-CredsToXMLRequest($genericPost)

    $DesiredIconData = @()
    #build desiredIconData and appName and append to XML request
    foreach ($icon in $app.Details.IconInfo.IconType) {
        $child = $genericPost.CreateElement("DesiredIconData")
        $xmlsize = $genericPost.CreateAttribute("size")
        $xmlsize.Value = $icon.size
        $xmlbpp = $genericPost.CreateAttribute("bpp")
        $xmlbpp.Value = $icon.bpp
        $xmlformat = $genericPost.CreateAttribute("format")
        $xmlformat.Value = $icon.format

        [void]$child.Attributes.Append($xmlsize)
        [void]$child.Attributes.Append($xmlbpp)
        [void]$child.Attributes.Append($xmlformat)
        [void]$genericPost.NFuseProtocol.RequestAppData.AppendChild($child)
    }

    #add AppName to XML
    $xmlElt = $genericPost.CreateElement("AppName")
    $xmlText = $genericPost.CreateTextNode($app.InName)
    [void]$xmlElt.AppendChild($xmlText)
    [void]$genericPost.NFuseProtocol.RequestAppData.AppendChild($xmlElt)

    return $genericPost
}

function Create-XMLCreds(){
    #creates credentials for use in Citrix XML POST requests
    #username
    [xml]$creds = ""
    $xmlCreds = $creds.CreateElement("Credentials")
    $xmlUserName = $creds.CreateElement("UserName")
    $xmlUsernameText = $creds.CreateTextNode($username)
    [void]$xmlUserName.AppendChild($xmlUsernameText)
    [void]$xmlCreds.AppendChild($xmlUserName)
    [void]$creds.AppendChild($xmlCreds)

    #password
    $xmlPassword = $creds.CreateElement("Password")
    $xmlEncoding = $creds.CreateAttribute("encoding")
    $xmlEncoding.Value = "ctx1"
    [void]$xmlPassword.SetAttributeNode($xmlEncoding)
    $xmlPasswordText = $creds.CreateTextNode($ctxpassword)
    [void]$xmlPassword.AppendChild($xmlPasswordText)
    [void]$xmlCreds.AppendChild($xmlPassword)
    [void]$creds.AppendChild($xmlCreds)

    #domain
    $xmlDomain = $creds.CreateElement("Domain")
    $xmlType = $creds.CreateAttribute("type")
    $xmlType.Value = "NT"
    [void]$xmlDomain.SetAttributeNode($xmlType)
    $xmlDomainText = $creds.CreateTextNode($domain)
    [void]$xmlDomain.AppendChild($xmlDomainText)
    [void]$xmlCreds.AppendChild($xmlDomain)
    [void]$creds.AppendChild($xmlCreds)

    return $creds
}

function Add-CredsToXMLRequest ([xml]$xmlRequest) {
    #adds Credentials to the appropriate location in the XML request
    [xml]$credentials = Create-XMLCreds

    if ($xmlRequest.NFuseProtocol.RequestAppData) {
        write-verbose "NFuseProtocol.RequestAppData"
        $credElm = $xmlRequest.CreateElement("Credentials")
        $credElm.InnerXml = $credentials.credentials.InnerXml
        [void]$xmlRequest.NFuseProtocol.RequestAppData.AppendChild($credElm)
    }

    if ($xmlRequest.PNAgentResourceRequest) {
    
        #add AppName to XML
        write-verbose "PNAgentResourceRequest"
        $xmlNode = $xmlRequest.SelectSingleNode("PNAgentResourceRequest")
        $credElm = $xmlRequest.CreateElement("Credentials")
        $credElm.InnerXml = $credentials.credentials.InnerXml
        [void]$xmlNode.AppendChild($credElm)
    }
    return [xml]$xmlRequest
}

function Add-AppNameToLaunch([xml]$listOfApps) {
    #we are going to query random apps for their icons and build the XML request.  We have to do this as not all
    #apps have a full suite of icons so we need to ensure we only query icons the app can provide
    $app = $listOfApps.NFuseProtocol.ResponseAppData.AppDataSet.AppData[(Get-Random -Minimum 0 -Maximum ($listOfApps.NFuseProtocol.ResponseAppData.AppDataSet.AppData.Count))]

#staticPNA Launch request
[xml]$genericPost = @'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE PNAgentResourceRequest SYSTEM "NFuse.dtd">
<PNAgentResourceRequest>
    <ClientName>PSStressTest</ClientName>
    <ClientAddress>10.10.10.10</ClientAddress>
    <LogonMethod>sson</LogonMethod>
    <ClientType>ica30</ClientType>
    <ICA_Options>
        <SpecialFolderRedirection>
            <Enabled>false</Enabled>
        </SpecialFolderRedirection>
    </ICA_Options>
</PNAgentResourceRequest>
'@

    Add-CredsToXMLRequest($genericPost)
    
    #add AppName to XML
    $xmlNode = $genericPost.SelectSingleNode("PNAgentResourceRequest")
    $xmlElt = $genericPost.CreateElement("Resource")
    $xmlText = $genericPost.CreateTextNode($app.InName)
    [void]$xmlElt.AppendChild($xmlText)
    [void]$xmlNode.AppendChild($xmlElt)
}


while ($true) {

    #use a default store if not specified in the command line
    if (-not($store)) {
        $store = "http://webinterface.bottheory.local/Citrix/PNAgentTrentent/"
    }

    $StartMs = Get-Date

    #properties for stats to export to CSV
    $prop = New-Object System.Object
    $prop  | Add-Member -type NoteProperty -name "Runtime" -value $StartMs
    $prop  | Add-Member -type NoteProperty -name "Store" -value $store

    #perf enhancement - disable invoke-webrequest progress bar
    $ProgressPreference = 'SilentlyContinue'

    <#
    Gets the client configuration.
    #>
    $stage = "Client Configuration"
    write-verbose "$stage"
    $headers = @{
    "User-Agent"="C:\PROGRA~2\Citrix\ICACLI~1\PNAMAIN.EXE"
    "Cache-Control"="no-cache"
    }


    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "config.xml") -Method GET -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "config.xml") -Method GET -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"


    <#
    Queries for Prelaunch applications.  I did not have any prelaunch applications to find out what it does
    if it actually gets a response.
    #>
    $stage = "PRELAUNCH"
    write-verbose "$stage"

    #remove byte order mark
    #web interface adds a BOM (byte order mark) to the config.xml response, Storefront does not.  We need to check for it
    #and remove it if necessary.  We "try" to convert the response to XML.  This will fail with WebInterface because of the BOM
    #but succeed with Storefront.  If it fails, we'll "catch it" and remove the BOM.
    try {
        [xml]$data = $webResult.Content
    }
    catch {
        $data = $webResult.Content
        $dataxml = [xml]$data.Substring(1)
        $data = $dataxml
    }
    
    $enumURL = $data.PNAgent_Configuration.Request.Enumeration.Location.'#text'

    [xml]$prelaunch = @'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="4.6">
	<RequestAppData>
		<Scope traverse="onelevel" type="PNFolder">$PRELAUNCH$</Scope>
		<DesiredDetails>permissions</DesiredDetails>
		<DesiredDetails>icon-info</DesiredDetails>
		<DesiredDetails>all</DesiredDetails>
		<ServerType>x</ServerType>
		<ServerType>win32</ServerType>
		<ClientType>ica30</ClientType>
		<ClientType>content</ClientType>
		<ClientName>PSStressTest</ClientName>
		<ClientAddress>10.10.10.10</ClientAddress>
	</RequestAppData>
</NFuseProtocol>
'@

    #add creds to XML POST request
    $post = Add-CredsToXMLRequest($prelaunch)

    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $post -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $post -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"

    <#
    Queries for a large dump of "all icons".  This request occurs every time.
    #>
    $stage = "Icon-Info All"
    write-verbose "$stage"

[xml]$getAllIcons = @'
<?xml version="1.0" encoding="UTF-8"?>
<NFuseProtocol version="4.6">
    <RequestAppData>
        <Scope traverse="onelevel" type="PNFolder" />
        <DesiredDetails>permissions</DesiredDetails>
        <DesiredDetails>icon-info</DesiredDetails>
        <DesiredDetails>all</DesiredDetails>
        <ServerType>x</ServerType>
        <ServerType>win32</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>content</ClientType>
        <ClientName>PSStressTest</ClientName>
        <ClientAddress>10.10.10.10</ClientAddress>
    </RequestAppData>
</NFuseProtocol>
'@

    $post = Add-CredsToXMLRequest($getAllIcons)

    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $post -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $post -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"

    #save the full list of applications we just enumerated
    [xml]$fullListOfApps = $webResult.content
    

    <#
    Request a application icon.  My testing has found this to occur with moderate frequency.
    #>
    $stage = "Icon-Individual Request1"
    write-verbose "$stage"

    $postBody = Generate-RandomIconRequest($fullListOfApps)

    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"

    <#
    Request a second application icon.  My testing has found this to occur with light frequency.
    #>
    $stage = "Icon-Individual Request2"
    write-verbose "$stage"
    $postBody = Generate-RandomIconRequest($fullListOfApps)
    write-verbose "Number of icon elements: $($postBody.NFuseProtocol.RequestAppData.DesiredIconData.size.count)"

    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "enum.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"


    <#
    Simulate an application launch by requesting an ICA file
    #>
    $stage = "Launch Application"
    write-verbose "$stage"

    $postBody = Add-AppNameToLaunch($fullListOfApps)
    $prop  | Add-Member -type NoteProperty -name "Application" -value $postBody.PNAgentResourceRequest.Resource 
     
    if ($stressComponent -eq $stage) {
    [console]::WriteLine("Measuring Response time of: $stage")
    [console]::WriteLine("Duration of task:")
        while ($true) {
            (measure-command {Invoke-WebRequest -Uri ($store + "launch.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult}).TotalSeconds
            write-debug "$webResult"
        } 
    } else {
        $duration = measure-command {Invoke-WebRequest -Uri ($store + "launch.aspx") -Method POST -Body $postBody -Headers $headers -ContentType "application/x-www-form-urlencoded" -WebSession $sfsession -UseBasicParsing -OutVariable webResult} -ErrorAction Stop
        $prop  | Add-Member -type NoteProperty -name "$stage" -value $duration.TotalSeconds
    }
    write-debug "$webResult"

    <#
    Export information to CSV
    #>
    $EndMs = Get-Date
    [console]::WriteLine("Loop took $($EndMs - $StartMs)")
    $prop  | Add-Member -type NoteProperty -name "Total Runtime" -value $($EndMs - $StartMs)
    $prop  | export-csv StressStorefront.csv -NoTypeInformation -Append

    #if loop is false terminate loop
    if ($loop -eq $false) {
        break
    }
}
</pre>

&nbsp;

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->