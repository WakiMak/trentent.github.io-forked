---
id: 584
title: Load testing Citrix XML broker
date: 2014-11-27T17:44:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/11/27/load-testing-citrix-xml-broker/
permalink: /2014/11/27/load-testing-citrix-xml-broker/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/11/load-testing-citrix-xml-broker.html
blogger_internal:
  - /feeds/7977773/posts/default/3033231245444974771
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - Performance

---
Previously, we encountered performance issues with the Citrix Web Interface due to our user load. Â I devised a test using the Microsoft WCAT to hammer the web interface servers. Â We found that after removing the ASPX processing limitation, logins were slow and we found that some XML brokers were taking a long time to respond.

I've been tasked with finding out why. Â The Citrix XML server is a basic web server that takes an XML post, processes it and spits back a XML file in response. Â To test the performance of the XML server I created a PowerShell script to send the same XML request that occurs when you login through the web interface.

XML-Test.ps1:

<div style="font-family: 'Lucida Console'; font-size: 9px;">
  <pre class="lang:ps decode:true ">$i=1
for ($i -le 5; $i++) {

$csv = import-csv list_of_xml.csv

$output = @();

foreach ($server in $csv) {
$serverName = $server.Broker
$serverPort = $server.Port


$uri = "http://{0}:{1}/scripts/wpnbr.dll" -f $serverName, $serverPort

$soap = @'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE NFuseProtocol SYSTEM "NFuse.dtd">
<NFuseProtocol version="5.4">
    <RequestAppData>
        <Scope traverse="subtree"></Scope>
        <DesiredDetails>permissions</DesiredDetails>
        <ServerType>all</ServerType>
        <ClientType>ica30</ClientType>
        <ClientType>content</ClientType>
        <Credentials>
            <UserName>trententtye</UserName>
            <Password encoding="ctx1">PASSWORDLOL</Password>
            <Domain type="NT">HEALTHY</Domain>
        </Credentials>
        <ClientName>WI_Sl2G71be93Q-ZNqOk</ClientName>
        <ClientAddress addresstype="dot">10.133.74.215</ClientAddress>
    </RequestAppData>
</NFuseProtocol>
'@

$numberOfThreads = Get-counter -Counter "\Citrix MetaFrame Presentation Server\Number of XML threads" -ComputerName $serverName
$numberOfBusy = Get-counter -Counter "\Citrix MetaFrame Presentation Server\Number of busy XML threads"  -ComputerName $serverName

#$command = Invoke-WebRequest $uri -Method post -ContentType "text/xml" -Body $soap
$results = measure-command {[xml]$WF = Invoke-WebRequest $uri -Method post -ContentType "text/xml" -Body $soap -TimeoutSec 120 -UseBasicParsing -UserAgent ""}
#$WF
$date = get-date
$date = "{0} {1}" -f $date.ToShortDateString(), $date.ToLongTimeString()
$output += "{0},{1},{2},{3},{4},{5}" -f $date,$serverName, $serverPort, $results.TotalMilliseconds, $numberOfThreads.CounterSamples.CookedValue, $numberOfBusy.CounterSamples.CookedValue
write-host $date,$serverName, $serverPort, $results.TotalMilliseconds, $numberOfThreads.CounterSamples.CookedValue, $numberOfBusy.CounterSamples.CookedValue
}



sleep 1

$output | out-file -append "\\wsctxapp301t\d$\WI_Load_Testing\OutFile.txt"

}</pre>
  
  <p>
    <span style="background-color: white;">Â </span></div> 
    
    <div>
    </div>
    
    <div>
      The list_of_xml.csv looks like this:
    </div>
    
    <div>
      <div>
        Farm,Broker,Port
      </div>
      
      <div>
        farm,wsctxrshipxml1,80
      </div>
    </div>
    
    <div>
    </div>
    
    <div>
      The output of the file looks like so:
    </div>
    
    <div>
    </div>
    
    <div>
      <div>
        11/26/2014 3:34:29 PM,wsctxrshipxml1,80,312.197
      </div>
      
      <div>
        11/26/2014 3:34:30 PM,wsctxrshipxml1,80,345.0929
      </div>
      
      <div>
        11/26/2014 3:34:31 PM,wsctxrshipxml1,80,255.165
      </div>
      
      <div>
        11/26/2014 3:34:33 PM,wsctxrshipxml1,80,294.3027
      </div>
      
      <div>
        11/26/2014 3:34:34 PM,wsctxrshipxml1,80,300.1806
      </div>
    </div>
    
    <div>
    </div>
    
    <div>
      The times are in milliseconds on the far right.
    </div>
    
    <div>
    </div>
    
    <div>
      Utilizing this information, we can gather how quickly the XML brokers respond and using this as a baseline we can start to load test to see the impact of how quickly the XML brokers will respond.
    </div>
    
    <div>
    </div>
    
    <div>
      To do this, we go back to WCAT. Â I set my XML.ubr file like so:
    </div>
    
    <div>
      <pre class="lang:default decode:true ">scenario {
    warmup    = 300;
    duration    = 300;
    cooldown    = 30;
    default {
        setheader {
            name    = "Connection";
            value    = "keep-alive";
        }

        version    = HTTP11;
        statuscode    = 0;
    }

    transaction {
        id = "PNA";
        weight = 1000;
        request {
            id = "1";
            url = "/scripts/wpnbr.dll";
            verb = POST;
                        port = 8080;
            postdata = "<?xml version=\"1.0\" encoding=\"UTF-8\"?><!DOCTYPE NFuseProtocol SYSTEM \"NFuse.dtd\"><NFuseProtocol version=\"5.4\"><RequestAppData><Scope traverse=\"subtree\"></Scope><DesiredDetails>permissions</DesiredDetails><ServerType>all</ServerType><ClientType>ica30</ClientType><ClientType>content</ClientType><Credentials><UserName>trententtye</UserName><Password encoding=\"ctx1\">PASSWORDLOL</Password><Domain type=\"NT\">HEALTHY</Domain></Credentials><ClientName>WSCTXAPP301T</ClientName><ClientAddress addresstype=\"dot\">10.132.169.130</ClientAddress></RequestAppData></NFuseProtocol>";
            setheader {
                name = "Content-Type";
                value = "text/xml";
            }
            setheader {
                name = "Connection";
                value = "Keep-Alive";
            }
            setheader {
                name = "Host";
                value = "wsctxrshipxml1";
            }
        }
    }
}
</pre>
    </div>
    
    <div>
    </div>
    
    <div>
      I then started the
    </div>
    
    <div>
      start "" wcclient.exe localhost
    </div>
    
    <div>
    </div>
    
    <div>
      And let it roll. Â When monitoring the server the process that takes up the most time is the IMASRV.EXE. Â I imagine that is because the XML service is really just a simple web server that accepts the traffic and hands it off to the IMASRV.exe to actually process and gets the response back and sends it back to the requestor.
    </div>
    
    <div>
    </div>
    
    <table style="margin-left: auto; margin-right: auto; text-align: center;" cellspacing="0" cellpadding="0" align="center">
      <tr>
        <td style="text-align: center;">
          <a style="margin-left: auto; margin-right: auto;" href="http://4.bp.blogspot.com/-p2HW3CyIqzY/VHdypLrFucI/AAAAAAAAArY/SOIfKt-Sax0/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B11.49.44%2BAM.png"><img src="http://4.bp.blogspot.com/-p2HW3CyIqzY/VHdypLrFucI/AAAAAAAAArY/SOIfKt-Sax0/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B11.49.44%2BAM.png" width="640" height="288" border="0" /></a>
        </td>
      </tr>
      
      <tr>
        <td style="text-align: center;">
          Started load testing at 11:49:00. Â After 40 concurrent connections XML service is responding to requests at 2000ms per request.
        </td>
      </tr>
    </table>
    
    <div>
      With this testing we can now try to improve the performance of the XML broker. Â We monitored one of our brokers and made some changes to it and reran the test to see the impact. Â The largest positive impact we saw was adding CPU's to the XML brokers. Â The following graphs illustrates the differences we saw:
    </div>
    
    <div>
    </div>
    
    <div style="clear: both; text-align: center;">
      <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-6eRv4CR3fQc/VHe0x2kL3zI/AAAAAAAAArs/hof7AKXV0ts/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B1.29.58%2BPM.png"><img src="http://4.bp.blogspot.com/-6eRv4CR3fQc/VHe0x2kL3zI/AAAAAAAAArs/hof7AKXV0ts/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B1.29.58%2BPM.png" width="640" height="368" border="0" /></a>
    </div>
    
    <p>
      &nbsp;
    </p>
    
    <div style="clear: both; text-align: center;">
      <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-GYWK5fYtovY/VHe0x-jcLiI/AAAAAAAAAro/no300MxSUWk/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B1.32.33%2BPM.png"><img src="http://2.bp.blogspot.com/-GYWK5fYtovY/VHe0x-jcLiI/AAAAAAAAAro/no300MxSUWk/s1600/Screen%2BShot%2B2014-11-27%2Bat%2B1.32.33%2BPM.png" width="640" height="369" border="0" /></a>
    </div>
    
    <div style="clear: both; text-align: center;">
    </div>
    
    <div style="clear: both; text-align: center;">
    </div>
    
    <div>
      I stopped the graph at around 20,000ms for responding to XML request for the top graph, and the maximum number of concurrent connections for the bottom graph at 3,000ms. Â 3,000ms would be very high for XML enumeration in my humble opinion, but still tolerable. Â On a single CPU system, XML enumeration can only sustain about 12-15 concurrent requests before it tops out, 2 CPU systems do slightly better at 24-28 concurrent requests and 4CPU at 60 concurrent requests. Â 8 CPU's and we exceed 3,000ms at 120 concurrent requests. Â Ideally, you would keep all requests under 1,000ms, which for 8 CPU is at 30 concurrent sessions, and 21 sessions for 4CPU. Â 1 CPU can only sustain about 2 concurrent requests to stay under 1,000ms while 2 CPU can sustain about 5 concurrent requests.
    </div>
    
    <div>
    </div>
    
    <div>
      Again, this is the same query that the web interface sends when you login to a farm. Â So if you have 10 farms and they all take 1,000ms to respond to an XML request you will sit at the login screen for 10 seconds. Â Storefront allows parallel requests which would reduce the time to 1 second (potentially) but for Web Interface (and even Store Front), having an optimized XML broker configuration is ideal and, apparently, is very dependent on the number of CPU's you can give it. Â Recommendation:
    </div>
    
    <div>
      As many as possible. Â Unfortunately, I did not have the ability to test 16 or 32 core systems but for an Enterprise environment, I would try to keep it at 8 as a minimum.
    </div>
    
    <!-- AddThis Advanced Settings generic via filter on the_content -->
    
    <!-- AddThis Share Buttons generic via filter on the_content -->