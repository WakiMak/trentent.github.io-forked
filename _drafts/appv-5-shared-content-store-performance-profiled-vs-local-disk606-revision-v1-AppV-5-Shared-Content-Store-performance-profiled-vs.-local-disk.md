---
id: 1059
title: AppV 5 Shared Content Store performance profiled vs. local disk
date: 2016-04-24T22:39:13-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/24/606-revision-v1/
permalink: /2016/04/24/606-revision-v1/
---
We are looking to utilize AppV 5&#8217;s shared content store and one of the things I was interested in was knowing what kind of overhead network performance may vs. local disk. Â What I have is a physical server with 3x300GB 10,000 RPM SAS drives in a RAID-5 vs. a CIFS share on SSD. Â The catch about the CIFS share is it is 300 kilometers (200 miles) away in another city. Â I used this share as I don&#8217;t have another share local to the physical box and this brings the performance of the share down, but it&#8217;s still faster than the disk. Â The IOMETER readings for the Shared Content Store and the local disk hosting the PackageInstallationRoot are:

<pre class="lang:default decode:true ">Local Disk (D:)
IOPS Â  Â  MB/s Â Avg IO/s Â Max IO latency Â %CPU
1324.44 Â 5.43 Â 24.123 Â  Â 451.5146 Â  Â  Â  Â 0.50%</pre>

&nbsp;

<pre class="lang:default decode:true ">CIFS Share
2803.2 Â 11.48 Â 11.41 Â  Â  4878.02 Â  Â  Â  Â  3.96%</pre>

IOMeter settings was 100% read with 4k transfer sizes

The CIFS share is 2x faster on average, pulling 2803 IOPS vs 1324. Â Bandwidth of the CIFS share is faster as well, 11.48MB/s vs. 5.43MB/s for the local disk.

To test the performance I did the following, I loaded an AppV 5 application to disk, I then wrote a AutoIT script to get the start time, launch the program, wait for the login screen, once the login screen is visible get the finish time. Â I then stopped and started the appvclient as this ensures nothing is cached in RAM and ran the AutoIT script again. Â I did this thirty times. Â I then turned on Shared Content Store (SCS) and loaded the package in that manner and retested. Â The results are (average of all 30 runs):

Local Disk: 9.6s  
SCS: 9.7s

For the 30 runs the Local Disk deviated +/- 0.2s from average so the range was from 9.4s to 9.8s.

For the SCS the deviation was much bigger, probably to be expected when you&#8217;re pulling from a file share 6ms away across a shared pipe. Â Deviation on the network was:  
+/- 1.85s with a range from 8.2s to 11.9s. Â Amazingly, there were numerous runs where the network bested the local disk under 9.4s for launch time. Â I&#8217;m sure if the SCS was local we&#8217;d get even more consistent performance and probably even faster performance. Â A local server to the share gets about 8x better IOMeter numbers.

Now, how about when the application has already been launched so files are cached? Â Results (average of all 30 runs):

Local Disk: 4.8s  
SCS: 4.8s

Local Disk deviation/range: +/- 1.4s, range 3.5-6.3s  
SCS deviation/range: +/- 1.85s, range 3.4s-7.1s

Again, the SCS has a bigger range but again pulls ahead of the local disk numerous times.

A curious thing I found was that after the 14th run in the loop the times improved greatly:

<pre class="lang:default decode:true ">Disk Start Finish Delta
Local D 15:58.0 16:10.7 00:12.7
Local D 16:10.9 16:17.1 00:06.2
Local D 16:17.2 16:23.4 00:06.3
Local D 16:23.5 16:29.6 00:06.1
Local D 16:29.7 16:35.9 00:06.2
Local D 16:36.0 16:42.1 00:06.1
Local D 16:42.2 16:48.2 00:06.1
Local D 16:48.3 16:54.5 00:06.2
Local D 16:54.6 17:01.5 00:06.9
Local D 17:01.5 17:07.6 00:06.1
Local D 17:07.7 17:13.7 00:06.1
Local D 17:13.8 17:20.3 00:06.5
Local D 17:20.3 17:26.4 00:06.1
Local D 17:26.5 17:32.5 00:06.0
Local D 17:32.5 17:38.1 00:05.5Â  Â //--speeds up here
Local D 17:38.1 17:41.7 00:03.5Â  Â \--speeds up here
Local D 17:41.8 17:45.3 00:03.5
Local D 17:45.4 17:48.9 00:03.5
Local D 17:49.0 17:52.8 00:03.9
Local D 17:52.9 17:56.4 00:03.5
Local D 17:56.5 18:00.0 00:03.5
Local D 18:00.1 18:03.6 00:03.6
Local D 18:03.7 18:07.3 00:03.6
Local D 18:07.3 18:11.1 00:03.7
Local D 18:11.1 18:14.7 00:03.5
Local D 18:14.7 18:18.2 00:03.5
Local D 18:18.3 18:22.1 00:03.8
Local D 18:22.1 18:25.7 00:03.6
Local D 18:25.7 18:29.3 00:03.6
Local D 18:29.4 18:33.0 00:03.7</pre>

I&#8217;m not sure if that&#8217;s the application or AppV has a caching structure but I&#8217;m leading towards AppV/Windows doing something with the file cache. Â I&#8217;m unsure how to prove this out.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->