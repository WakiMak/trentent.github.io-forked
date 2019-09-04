---
id: 1958
title: Lets Make PVS Target Device Booting Great Again (Part 3)
date: 2017-01-13T15:49:54-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=1958
permalink: /?p=1958
categories:
  - Uncategorized
---
&nbsp;

In attempt to focus on the network preferences within PVS I&#8217;ve setup a 30GB RAMDisk on a file server on the same Hyper-V host. Â My hope is I can eliminate disk speed entirely from the equation and we can focus on what factors impact the StreamProcess from a the options it presents for what it can do with the network.

The values that are available:

<img class="aligncenter size-full wp-image-1959" src="http://theorypc.ca/wp-content/uploads/2017/01/Options.png" alt="" width="478" height="614" srcset="http://theorypc.ca/wp-content/uploads/2017/01/Options.png 478w, http://theorypc.ca/wp-content/uploads/2017/01/Options-234x300.png 234w" sizes="(max-width: 478px) 100vw, 478px" /> 

&nbsp;

What I&#8217;ve done is configured my PVS server to the bare minimum, with the idea that modifying these values will allow us to measure their impact according to their described ability. Â The help file explains what these options do:

> **Threads per port** â€” Number of threads in the thread pool that service UDP packets received on a given UDP port. Between four and eight are reasonable settings. Larger numbers of threads allow more target device requests to be processed simultaneously, but is consumes more system resources.
> 
> **Buffers per thread** â€” Number of packet buffers allocated for every thread in a thread pool. The number of buffers per thread should be large enough to enable a single thread to read one IO transaction from a target device. So buffers per threads should ideally be set to (IOBurstSize / MaximumTransmissionUnit) + 1). Setting the value too large consumes extra memory, but does not hurt efficiency. Setting the value too small consumes less RAM, but detrimentally affects efficiency.
> 
> **Server cache timeout** â€” Every server writes status information periodically to the Provisioning Services database. This status information is time-stamped on every write. A server is considered â€˜Upâ€™ by other servers in the farm, if the status information in the database is newer than the Server cache timeout seconds. Every server in the farm will attempt to write its status information every (Server cache timeout/2) seconds, i.e. at twice the timeout rate. A shorter server cache timeout value allows servers to detect offline servers more quickly, at the cost of extra database processing. A longer Server cache timeout period reduces database load at the cost of a longer period to detect lost servers.
> 
> **Local and Remote Concurrent I/O limits** â€” Controls the number of concurrent outstanding I/O transactions that can be sent to a given storage device. A storage device is defined as either a local drive letter (C: or D: for example) or as the base of a UNC path, for example \\ServerName.
> 
> Since the PVS service is a highly multi-threaded service, it is possible for it to send hundreds of simultaneous I/O requests to a given storage device. These are usually queued up by the device and processed when time permits. Some storage devices, Windows Network Shares most notably, do not deal with this large number of concurrent requests well. They can drop connections, or take unrealistically long to process transactions in certain circumstances. By throttling the concurrent I/O transactions in the PVS Service, better performance can be achieved with these types of devices.
> 
> Local device is defined as any device starting with a drive letter. Remote is defined as any device starting with a UNC server name. This a simple way to achieve separate limits for network shares and for local drives.
> 
> If you have a slow machine providing a network share, or slow drives on the machine, then a count of 1 to 3 for the remote limit may be necessary to achieve the best performance with the share. If you are going to fast local drives, you might be able to set the local count fairly high. Only empirical testing would provide you with the optimum setting for a given hardware environment. Setting either count to 0 disables the feature and allows the PVS Service to run without limits. This might be desirable on very fast local drives.
> 
> If a network share is overloaded, youâ€™ll see a lot more device retries and reconnections during boot storms. This is caused by read/write and open file times > 60 seconds. Throttling the concurrent I/O transactions on the share reduces these types of problems considerably.

&nbsp;

I&#8217;m going to try and take a look at a few factors in this post. Â I&#8217;m going to look at 3 different types of storage:

  1. NAS (iSCSI)
  2. PCIe SSD
  3. RAM Disk

For each type I&#8217;m going to examine the performance impact of having the storage local to the PVS server and remote on a SMB share. Â For the SMB share I&#8217;m going to look at the impact of adding latency to the share. Â This will have the effect of &#8216;slowing&#8217; down the share which should mean I can simulate different types of storage.

And we&#8217;ll measure the impact of each on boot performance.

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->