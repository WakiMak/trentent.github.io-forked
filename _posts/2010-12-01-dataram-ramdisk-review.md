---
id: 716
title: Dataram RAMDisk Review
date: 2010-12-01T23:43:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2010/12/01/dataram-ramdisk-review/
permalink: /2010/12/01/dataram-ramdisk-review/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2010/12/dataram-ramdisk-review.html
blogger_internal:
  - /feeds/7977773/posts/default/5002701594216695672
categories:
  - Blog
  - Uncategorized
tags:
  - Uncategorized
---
<div style="text-align: center;">
  <span><u><br /></u></span>
</div>



<div style="text-align: center;">
  <span><u><br /></u></span>
</div>



<div style="text-align: left;">
  I&#8217;ve purchased a product called Dataram RAMDISK and am going to do a quick review on it. It&#8217;s free for RAM Disk&#8217;s up to 4GB in size. Beyond that size and you need to pay $9.99 for a license file that allows you to increase it to whatever amount of free RAM you have in your system up to a maximum of 64GB. My system is a P6T SE with 6 RAM slots, 3x2GB RAM and 3x1GB RAM sticks are installed, giving me 9GB in total. This configuration activates triple-channel mode, so I&#8217;m getting the full 192bit of available bandwidth from my memory.
</div>

<div>
</div>

<div>
  All of my RAM is DDR3 @ 1333MHz (PC3-10600), but Everest is reporting a speed of 800MHz. My theoretical MB/s speed should max out at 32000MB/s, but if Everest is only operating at 800MHz that means my theoretical maximum speed is 19200MB/s. I think the reason I did this is because my triple channel puked at the unmatched channels causing BSOD&#8217;s and non-boots, but I&#8217;ll investigate later as it&#8217;s a huge performance penalty. Everest confirms this is the measured speed I&#8217;m running at 19200MB/s
</div>

<div>
</div>

<div>
  <a href="http://3.bp.blogspot.com/_E0GX9VvxUBU/TPc5IZwwgNI/AAAAAAAAAFg/Bmby28E2hZo/s1600/Everest-PC8500.png"><img src="http://3.bp.blogspot.com/_E0GX9VvxUBU/TPc5IZwwgNI/AAAAAAAAAFg/Bmby28E2hZo/s400/Everest-PC8500.png" border="0" alt="" id="BLOGGER_PHOTO_ID_5545964282647183570" style="display: block; margin-top: 0px; margin-right: auto; margin-bottom: 10px; margin-left: auto; text-align: center; cursor: pointer; width: 321px; height: 145px; " /></a>
</div>

<div style="text-align: center;">
  <span style="font-size: x-small;">(Everest measures 19198MB/s)</span>
</div>

<div>
</div>

<div>
  <a href="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc566dNrDI/AAAAAAAAAFo/7-9Jlh_FH_s/s1600/RAM-Types.png"><img src="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc566dNrDI/AAAAAAAAAFo/7-9Jlh_FH_s/s400/RAM-Types.png" border="0" alt="" id="BLOGGER_PHOTO_ID_5545965150417038386" style="display: block; margin-top: 0px; margin-right: auto; margin-bottom: 10px; margin-left: auto; text-align: center; cursor: pointer; width: 300px; height: 122px; " /></a>
</div>

<div style="text-align: center;">
  <span style="font-size: x-small;">(My unmatched odd sets of RAM pairs. Note, 1333MHz is the lowest my RAM goes, to 1800MHz for the fastest).</span>
</div>

<div>
</div>

<div>
</div>

<div>
</div>

<div>
  With that said, my current theoretical maximum speed my RAM should be able to operate at is 19200MB/s. I&#8217;m going to test my RAM first to gauge it&#8217;s maximum speed, then test the RAM Disk to see if it matches up.
</div>

<div>
</div>

<div style="text-align: center;">
  <a href="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc-ZuaufCI/AAAAAAAAAFw/zxBUkg3irEY/s1600/Memory-Benchmark.png"><span style="color: rgb(0, 0, 0); -webkit-text-decorations-in-effect: none; "></span></a><a href="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc-ZuaufCI/AAAAAAAAAFw/zxBUkg3irEY/s1600/Memory-Benchmark.png"><img src="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc-ZuaufCI/AAAAAAAAAFw/zxBUkg3irEY/s400/Memory-Benchmark.png" border="0" alt="" id="BLOGGER_PHOTO_ID_5545970077807836194" style="display: block; margin-top: 0px; margin-right: auto; margin-bottom: 10px; margin-left: auto; text-align: center; cursor: pointer; width: 400px; height: 352px; " /></a>
</div>

<div>
</div>

<div>
  Everest reports that my actual performance is 11446MB/s Read, 11978MB/s Write and 15376MB/s Copy. The Memory Read benchmark reads a 16 MB sized, 1 MB aligned data buffer from system memory into the CPU. Memory is read in forward direction, continuously without breaks. The Memory Write benchmark writes a 16 MB sized, 1 MB aligned data buffer from the CPU into the system memory.
</div>

<div>
</div>

<div>
  I will attempt to duplicate these with IO Meter to see how close the RAMDisk can get to the theoretical benchmark.
</div>

<div>
</div>

<div>
  Before that, he&#8217;s Everest Disk Benchmark result (everything is automatic &#8211; Linear Read):
</div>

<div style="text-align: center;">
  <a href="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPc-ZuaufCI/AAAAAAAAAFw/zxBUkg3irEY/s1600/Memory-Benchmark.png"><br /></a>
</div>

<div>
  <a href="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPdBi6dBvSI/AAAAAAAAAF4/XS2wMhLePAQ/s1600/linear-read.png"><img src="http://2.bp.blogspot.com/_E0GX9VvxUBU/TPdBi6dBvSI/AAAAAAAAAF4/XS2wMhLePAQ/s400/linear-read.png" border="0" alt="" id="BLOGGER_PHOTO_ID_5545973534192418082" style="display: block; margin-top: 0px; margin-right: auto; margin-bottom: 10px; margin-left: auto; text-align: center; cursor: pointer; width: 400px; height: 128px; " /></a>
</div>

<div>
</div>

<div>
  With IOMeter I have the following configuration:
</div>

<div>
  Size 1MB
</div>

<div>
  Access 100%
</div>

<div>
  Read 100%
</div>

<div>
  Burst 1
</div>

<div>
  Alignment 1MB
</div>

<div>
  100% Sequential
</div>

<div>
  Align I/O&#8217;s 1MB
</div>

<div>
</div>

<div>
  The results I get are:
</div>

<div>
  5000 I/O per second
</div>

<div>
  5000 MB/s
</div>

<div>
</div>

<div>
  Swapping Read 100% to 0% (making it 100% Write) I get:
</div>

<div>
  4930 I/O and MB/s
</div>

<div>
</div>

<div>
  So writes are more intensive then reads, as they usually are, but I cannot get anywhere close to the speed of reading a 16MB sized file in 1MB chunks to the maximum theoretical speed of the RAM. I&#8217;m unsure as to why that may be, but I suspect it&#8217;s the path the file takes from the RAMDisk -> Driver -> Bus -> CPU&#8230;?
</div>

<div>
</div>

<div>
  To try and maximize the speed of the drive and match up to Everest Linear read to gauge the maximum speed of the disk, I&#8217;ll setup IO Meter to match Everest&#8217;s description of the Linear Read test:
</div>

<div>
  <div>
    <blockquote>
      <p>
        This test is designed to measure the sustained linear (sequential) reading performance of the storage device by reading all data from the surface of the device.
      </p>
    </blockquote>
  </div>
</div>

<div>
</div>

<div>
  IOMeter does this easily enough.
</div>

<div>
</div>

<div>
  Using Procmon, I was able to verify that Linear Read uses a 64KB block size and reads the entire disk. Doing this with IO Meter I achieved the same results as above in the MB/s, but much higher IO/s. I&#8217;m unsure why IOMeter is unable to match Everest&#8217;s results.
</div>

<div>
</div>

<div>
  HD Tach was not useful as it would operate at 3000MB/s for various lengths of time each time I reran the test. Sometimes it would operate at 3000MB/s up to 0.5GB then 700MB/s for the rest of the drive, or 3000MB/s up to 2.4GB.
</div>

<div>
</div>

<div>
  Too inconsistent to make an observation.
</div>

<div>
</div>

<div>
  To compare this RAMDisk to other HDD&#8217;s I turned to Anandtech and used his settings to compare some SSD drives. <a href="http://www.anandtech.com/show/2614/8">His results are here.</a>
</div>

<div>
</div>

<div>
  The results I achieved are:
</div>

<div>
  133362 IOs per second
</div>

<div>
  522 MB/s
</div>

<div>
  Average Write Latency 0.0073
</div>

<div>
  Max Write Latency 0.5526
</div>

<div>
</div>

<div>
  Compared to the first gen Intel SSD&#8217;s the RAM Disk is:
</div>

<div>
  <div>
    12x faster in IOs per second
  </div>
  
  <div>
    12x faster in MB/s
  </div>
  
  <div>
    12x faster in Average Write Latency 0.0073
  </div>
  
  <div>
    170x faster in Max Write Latency 0.5526
  </div>
</div>

<div>
</div>

<div>
  Pretty much an order of magnitude faster as should be expected for a RAM Disk vs. a top of the line SSD.
</div>

<div>
</div>

<div>
  In the end, I&#8217;m unsure if I&#8217;ve hit a wall with my numbers, but it feels artificial. Everest does come within 70% of the numbers it achieved in a pure memory test, but even the Everest test tops out at exactly 8192MB/s.
</div>

<div>
</div>

<div>
  I&#8217;ll try changing my memory speeds to something faster and see if this limit is artificial or a limit of my system.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->