---
id: 2388
title: Citrix Storefront ‚Äì Performance Testing and Tuning ‚Äì Part 4 ‚Äì PerfMon Counters, PNA Logon
date: 2017-06-02T07:59:28-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2388
permalink: /2017/06/02/citrix-storefront-performance-testing-and-tuning-part-4-perfmon-counters-pna-logon/
image: /wp-content/uploads/2017/06/000_Ica_h32bit_256.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - PowerShell
  - scripting
  - Storefront
---
In looking at the performance of the Citrix Storefront Server, one of the thing I want is to understand what Storefront is doing at each stage of a session life cycle. ¬ There are two session-types that I‚Äôm curious about, a web browser based connection and a PNA connection. ¬ I‚Äôm going to examine a PNA¬ based connection in this post. ¬ See this post for a browser based examination.

Using my powershell script to simulate a user connection I put ‚Äòpauses‚Äô between each stage. ¬ I then setup Perfmon to capture counters from the following objects:

<div id="crayon-59307c29ba4eb079012780" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <pre class="lang:default decode:true">Citrix Dazzle Resources Controller\Image Response Whole Body  Calls / second
Citrix Dazzle Resources Controller\Image Response Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\List Sessions Whole Body  Calls / second
Citrix Dazzle Resources Controller\List Sessions Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\List Whole Body  Calls / second
Citrix Dazzle Resources Controller\List Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\ListWithoutAutoProvision Whole Body  Calls / second
Citrix Dazzle Resources Controller\ListWithoutAutoProvision Whole Body Average Time (Microseconds)
Citrix Dazzle Resources Controller\Update Resources Image Cache  Calls / second
Citrix Dazzle Resources Controller\Update Resources Image Cache Average Time (Microseconds)
Citrix Delegated Explicit Authentication\Authenticate  Calls / second
Citrix Delegated Explicit Authentication\Authenticate Average Time (Microseconds)
Citrix Delegated Kerberos Authentication\Authenticate  Calls / second
Citrix Delegated Kerberos Authentication\Authenticate Average Time (Microseconds)
Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(authservice:tokenservices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(citrixfederationauthentication:authenticate:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(dazzleresources:launchica:post:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(dazzleresources:list:get:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_agservices)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_agservices)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_configuration)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_configuration)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_roaming)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_roaming)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_store)\Controller Action  Calls / second
Citrix Delivery Services Web Application(endpoints:endpoints:get:_citrix_store)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(protocols:choices:post:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(selfserviceaccountmanagement:allowselfserviceaccountmanagement:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Delivery Services Web Application(tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action  Calls / second
Citrix Delivery Services Web Application(tokenvalidation:tokenvalidation:get:_citrix_authentication)\Controller Action Average Time (Microseconds)
Citrix Receiver for Web\Get Ica file  Calls / second
Citrix Receiver for Web\Get Ica file Average Time (Microseconds)
Citrix Receiver for Web\Get icon  Calls / second
Citrix Receiver for Web\Get icon Average Time (Microseconds)
Citrix Receiver for Web\Get launch status  Calls / second
Citrix Receiver for Web\Get launch status Average Time (Microseconds)
Citrix Receiver for Web\List resources  Calls / second
Citrix Receiver for Web\List resources Average Time (Microseconds)
Citrix Receiver for Web\Login attempts  Calls / second
Citrix Receiver for Web\Login attempts Average Time (Microseconds)
Citrix Resource Subscription\Dispose  Calls / second
Citrix Resource Subscription\Dispose Average Time (Microseconds)
Citrix Resources Common\Find all resources  Calls / second
Citrix Resources Common\Find all resources Average Time (Microseconds)
Citrix Resources Common\Find resource by id  Calls / second
Citrix Resources Common\Find resource by id Average Time (Microseconds)
Citrix Resources Common\ICA Launch  Calls / second
Citrix Resources Common\ICA Launch Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.11)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.11)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.12)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.12)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.13)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(10.10.10.14)\Network Traffic  Calls / second
Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic  Calls / second
Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Average Time (Microseconds)
Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic  Calls / second
Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Average Time (Microseconds)</pre>
  
  <p>
    I‚Äôve added 6 farms to the storefront server to examine the load ‚Äúin a real world‚Äù environment as Storefront does do some magic with concurrent enumeration. ¬ What I love that Citrix has done, is actually time the transactions AND gives you the ‚Äòrate‚Äô the transactions are occurring at. ¬ This will make it much easier to baseline what your load is vs how I had to do it to measure the ‚Äòrates‚Äô for Web Interface.
  </p>
  
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <p>
    <span style="color: #ff0000;">$stage = "Client Configuration"</span><br /> $store + <span style="color: #3366ff;">"config.xml"</span>
  </p>
  
  <p>
    So for a web browser based connection, I ‚Äòconnected‚Äô to the site and saw nothing in the perf counters. ¬ Storefront does very little work for the initial connection.
  </p>
  
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <span style="color: #ff0000;">$stage = "PRELAUNCH"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  $store + <span style="color: #3366ff;">"enum.aspx"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  POST with:
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
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
</NFuseProtocol></pre>
  
  <p>
    Response:
  </p>
  
  <pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.8">
    <ResponseAppData>
    </ResponseAppData>
</NFuseProtocol></pre>
  
  <p>
    I do ¬ not have any PRELAUNCH applications.
  </p>
  
  <p>
    However, we do get perfmon counter data with this request:
  </p>
  
  <p>
    Citrix Resource Subscription\Dispose Calls / second : 1<br /> Citrix Delegated Explicit Authentication\Authenticate Calls / second : 1<br /> Citrix Delegated Explicit Authentication\Authenticate Average Time (Microseconds) : 95,392<br /> Citrix Resources Common\Find all resources ¬ Average Time (Microseconds)¬ : 57,277<br /> Citrix Resources Common\Find all resources ¬ Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds) : 36,383<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) : 47,260<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) : 60,759<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 30,730<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Average Time (Microseconds) : 44,155
  </p>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <span style="color: #ff0000;">$stage = "Icon-Info All"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  $store + <span style="color: #3366ff;">"enum.aspx"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  POST with:
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <pre class="lang:xhtml decode:true "><?xml version="1.0" encoding="UTF-8"?>
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
</NFuseProtocol></pre>
  
  <p>
    Response (truncated):
  </p>
  
  <pre class="lang:default decode:true"><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.8">
    <ResponseAppData>
        <AppDataSet>
            <Scope traverse="onelevel" type="PNFolder"></Scope>
            <AppData type="app">
                <InName>CTX:Microsoft Office - Word</InName>
                <FName>Microsoft Office - Word</FName>
                <Details>
                    <Settings appisdisabled="false" appisdesktop="false">
                        <Folder>Office\Analyst</Folder>
                        <Description>Microsoft Office - Word</Description>
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
                        <PublisherName>CTX</PublisherName>
                        <SSLEnabled>false</SSLEnabled>
                        <RemoteAccessEnabled>false</RemoteAccessEnabled>
                    </Settings>
                    <Icon>AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAAAAAAAAAAAAAAACAAIAAABAAAAAAAAAAAAAAAAAAAAAAAAIAAAAAAAEAAAAAAABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAIAAAAAAAAAIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIPPPPPIPPPPPPPPPPPPPPPPPPPPPPPPIIPPPPIHIIIIIPPPPPPPPPPPPPPPPPPPIIPPPPHHHIHIHIHIIIIIPPPPPPPPPPPPIIPPPPHHIIIIIIPIIIIIHIIIIIPPPPPPIIPPPIHHHIIIIIIPPPPPPIIIIHIPIPPPIIPPPIHHHIHIHIIPPPPIPPPPIPPPIPPPIIPPPIHHHIHIIIIPIPIPIIIPIIPIIPPPIIPPPIHHHIIHHHIIPIPIPIIIIIPIPPPPIIPPPIHHHHHIHHIIIIIIIIIPIIIIPPPPIIPPPIHHGHHHHHIIIIIIIIIIIIPIPPPPIIPPIHHHHGGGHHHHIIHIIHHIHHPIPPPPIIPPIHHHGHGGGHHHHHHHHHHHIHIIPPPPIIPPIHGHHGGGGGHHHHHHHHHHHHPIPPPPIIPPIEHGHGGGGGGEGGHHGHGHHIIIPPPPIIPPIBEEGEEEGGBBGGGGGGGHGIIIPPPPIIPPHEEEBGEEEBJEGGGGGGHGHIIPPPPPIIPPIHGEEEEEAJJEGGGGGGGGGIIIPPPPIIPIHIIIHHHEBJJEGGGGGGGHGIIPPPPPIIPPPIIIIIIBJJJAGGEGGGGGGAIPPPPPIIPPPPPPIIIHAJJJBHIHHGGGGBIIPPPPIIPPPPPPPPPIBJJJJBHIIIIIHBIIIPPPIIPPPPPPPPPIBJJJJJJBBBIBIBJIIHPPIIPPPPPPPPIIHAJJJJJJJJJJJJJJIIHPIIPPPPPPPIIIIIAJJJJJJJJJJJJJJIIHIIPPPPPPPIIIIPIBAJJJJJJJJJJJJJJBIIPPPPPPPIIIIIPPIBBBBJJJJJJJJJBIIIPPPPPPPPIIPPIPPPIIBHBHHBJJJBIPIIPPPPPPPPPPIIPIIIIIIIPPPBJBBPPPIIPPPPPPPPPPPPPPPPPPPPPPPBBHPPPPIIPPPPPPPPPPPPPPPPPPPPPPPAIPPPPPIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIII</Icon>
                    <IconInfo>
                        <IconType size="32" bpp="32" format="raw">
                        </IconType>
                        <IconType size="16" bpp="32" format="raw">
                        </IconType>
                        <IconType size="32" bpp="4" format="raw">
                        </IconType>
                    </IconInfo>
                </Details>
                <SeqNo>1490021516</SeqNo>
                <ServerType>win32</ServerType>
                <ClientType>ica30</ClientType>
            </AppData>
        </AppDataSet>
    </ResponseAppData>
</NFuseProtocol></pre>
  
  <p>
    And the Perfmon counter data:
  </p>
  
  <p>
    Citrix Resource Subscription\Dispose¬ Average Time (Microseconds) : 1<br /> Citrix Resource Subscription\Dispose Calls / second : 1<br /> Citrix Delegated Explicit Authentication\Authenticate Calls / second : 1<br /> Citrix Delegated Explicit Authentication\Authenticate Average Time (Microseconds) :¬ 88,473<br /> Citrix Resources Common\Find all resources ¬ Average Time (Microseconds)¬ :¬ 6,314,583<br /> Citrix Resources Common\Find all resources ¬ Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds) :¬ 42,697<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) :¬ 37,653<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) :¬ 64,622<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 125,629<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 191,408
  </p>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <span style="color: #ff0000;">$stage = "Icon-Individual Request1"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  $store + <span style="color: #3366ff;">"enum.aspx"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <p>
    POST data:
  </p>
  
  <pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
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
		<Credentials>
			<UserName>trententtye</UserName>
			<Password encoding="ctx1">JJJHFTEBJKAKLSNVYRHABEHBDHBTTEJWUDBJLOLEO</Password>
			<Domain type="NT">BOTTHEORY.LOCAL</Domain></Credentials>
			<DesiredIconData size="32" bpp="4" format="raw" />
			<DesiredIconData size="48" bpp="32" format="raw" />
			<DesiredIconData size="32" bpp="32" format="raw" />
			<DesiredIconData size="16" bpp="32" format="raw" />
			<AppName>CTX:Microsoft Office - Word</AppName>
	</RequestAppData>
</NFuseProtocol></pre>
  
  <p>
    Response:
  </p>
  
  <pre class="lang:xhtml decode:true"><?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.8">
    <ResponseAppData>
        <AppDataSet>
            <Scope traverse="onelevel" type="PNFolder"></Scope>
            <AppData type="app">
                <InName>CTX:Microsoft Office - Word</InName>
                <FName>Microsoft Office - Word</FName>
                <Details>
                    <Settings appisdisabled="false" appisdesktop="false">
                        <Description>Microsoft Office - Word</Description>
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
                <IconData size="32" bpp="4" format="raw">////////////////AAAAAwAA3cAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA==</IconData>
                <IconData size="48" bpp="32" format="raw">////////AAD///////8AAP///////wAA////////AAD///////8AAIAAAAAAAQAAgAAAAAABAACAAAAAAAEAAIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA</IconData>
                <IconData size="32" bpp="32" format="raw">////////////////AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA</IconData>
                <IconData size="16" bpp="32" format="raw">//8AAP//AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA//8AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA</IconData>
                <SeqNo>1408469520</SeqNo>
                <ClientType>ica30</ClientType>
            </AppData>
        </AppDataSet>
    </ResponseAppData>
</NFuseProtocol></pre>
  
  <p>
    And the Perfmon counter data:
  </p>
  
  <p>
    Citrix Resource Subscription\Dispose¬ Average Time (Microseconds) : 0.5<br /> Citrix Resource Subscription\Dispose Calls / second :¬ 2<br /> Citrix Delegated Explicit Authentication\Authenticate Calls / second :¬ 2<br /> Citrix Delegated Explicit Authentication\Authenticate Average Time (Microseconds) :¬ 140,062<br /> Citrix Resources Common\Find all resources ¬ Average Time (Microseconds)¬ :¬ 219,619<br /> Citrix Resources Common\Find all resources ¬ Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Calls / second :¬ 1<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Calls / second : 1<br /> Citrix Xml Service Communication(10.10.10.13)\Network Traffic Average Time (Microseconds) :¬ 44,896<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) :¬ 40,508<br /> Citrix Xml Service Communication(10.10.10.14)\Network Traffic Average Time (Microseconds) :¬ 36,124<br /> Citrix Xml Service Communication(&5.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 127,686<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 165,869
  </p>
  
  <p>
    I feel like I'm detecting a theme here...
  </p>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <span style="color: #ff0000;">$stage = "Launch Application"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  $store + <span style="color: #3366ff;">"launch.aspx"</span>
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  This launch only hits the specific XML service from Storefront now:
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Citrix Resource Subscription\Dispose¬ Average Time (Microseconds) :¬ 1<br /> Citrix Resource Subscription\Dispose Calls / second :¬ 1<br /> Citrix Delegated Explicit Authentication\Authenticate Calls / second : 1<br /> Citrix Delegated Explicit Authentication\Authenticate Average Time (Microseconds) :¬ 88,206
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Citrix Resources Common\ICA Launch Average Time (Microseconds) :¬ 260,238
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Citrix Resources Common\ICA Launch Calls / second¬ :¬ 1<br /> Citrix Resources Common\Find resource by id Average Time (Microseconds) : 217,447
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Citrix Resources Common\Find resource by id Calls / second¬ :¬ 1
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Citrix Xml Service Communication(10.10.10.13)\Network Traffic Calls / second :¬ 4<br /> Citrix Xml Service Communication(&65t.bottheory.local)\Network Traffic Average Time (Microseconds) :¬ 142,010
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  <hr />
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  Final results:
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  When PNA is used, it makes POST commands to the Storefront web services that then forwards those requests to the XML brokers, compiles the results and sends them back. ¬ It's pretty obvious that the performance of the brokers is imperative for PNA to operate efficiently.
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  I kind of wish¬ Storefront would break down the XML perfmon requests into their sub categories. ¬ By averaging out all requests to the XML brokers, it would make it difficult to pinpoint which sequence of events may be causing your pain point. ¬ The values will allow to target a specific XML broker for further analysis, but some¬ it could have been further helpful if you knew specifically that the "&5.bottheory.local" broker was 10x longer to enumerate your full application icon list. ¬ An average could 'hide' that bottleneck. ¬ My account has over 400 published applications and gets 100 different individual icon requests after the 'icon-all' request. ¬ If this counter truly measures the average it would obscure the high 'icon-all' time as 100 instances of would outweigh the single metric.
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  However, this is still fantastic information. ¬ I have written a couple scripts for 'querying' each stage to measure the response time of Storefront/Web Interface and Citrix has effectively added the ability to just pull that information from real users using these counters! ¬ It's now easier and more important than ever to monitor your XML brokers from Storefront to ensure you are running at optimal efficiency.
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  To put these counters in context, the 'Network Traffic Calls / second' and 'Network Traffic Calls / Total' will tell you which of your XML brokers are getting hit the most. ¬ The Total will give you that big picture, "this guy gets hit alot" and the per second counter will give you that real time data.
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
</div>

<div class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
  The "Network Average Time" will give you the data on how long or how hard that particular broker is working. ¬ The higher the number the more work it's doing or the more you should focus on optimizing it. ¬ I don't have a guide for what these numbers should be, as different users will have different expectations. ¬ But I have shown what the values we get and I would argue we get great performance so I would try to keep your numbers below mine üôÇ
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->