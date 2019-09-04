---
id: 1203
title: Windows Backup Error 0x81000019
date: 2016-04-25T11:54:58-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/712-revision-v1/
permalink: /2016/04/25/712-revision-v1/
---
Recently, I&#8217;ve been getting an error from Windows Backup:

<div>
</div>

<div>
  Error Code: 0x81000019
</div>

<div>
</div>

<div>
</div>

> <div>
>   <div>
>     <span class="Apple-style-span">Event Viewer lists the following additional information:</span>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Shadow copy creation failed because of error reported by ASR Writer. More info: The requested system device cannot be found. (0x80073BC3).</span>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Â </span>
>   </div>
>   
>   <div>
>     <div>
>       <span class="Apple-style-span">Volume Shadow Copy Service warning: ASR writer Error 0x80073bc3. hr = 0x00000000, The operation completed successfully.</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">.</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Operation:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">PrepareForBackup event</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Context:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Execution Context: ASR Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Execution Context: Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Class Id: {be000cbe-11fe-4426-9c58-531aa6355fc4}</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Name: ASR Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Instance ID: {5c8b67a8-a665-45e5-9f5c-45382f136693}</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Error-specific details:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">ASR Writer: The requested system device cannot be found. (0x80073BC3)</span>
>     </div>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Â </span>
>   </div>
>   
>   <div>
>     <div>
>       <span class="Apple-style-span">Volume Shadow Copy Service error: Unexpected error calling routine Check OnIdentifyError. hr = 0x80073bc3, The requested system device cannot be found.</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">.</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Operation:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">PrepareForBackup event</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Context:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Execution Context: ASR Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Execution Context: Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Class Id: {be000cbe-11fe-4426-9c58-531aa6355fc4}</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Name: ASR Writer</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Writer Instance ID: {5c8b67a8-a665-45e5-9f5c-45382f136693}</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Error-specific details:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">ASR Writer: The requested system device cannot be found. (0x80073BC3)</span>
>     </div>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Â </span>
>   </div>
>   
>   <div>
>     <div>
>       <span class="Apple-style-span">Fault bucket 668258104, type 5</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Event Name: WindowsBackupFailure</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Response: Not available</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Cab Id: 0</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Problem signature:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P1: Backup</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P2: 6.1.7600</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P3: 0x81000019</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P4: 7</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P5:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P6:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P7:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P8:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P9:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">P10:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Attached files:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">C:\Windows\Logs\WindowsBackup\WindowsBackup.1.etl</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">These files may be available here:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">C:\ProgramData\Microsoft\Windows\WER\ReportArchive\NonCritical_Backup_7a9178ddfcd376c581a8653b09ae5e2464735bf_100ef6dc</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Â </span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Analysis symbol:</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Rechecking for solution: 0</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Report Id: 8b57e781-0633-11e0-a042-90e6ba2d22c8</span>
>     </div>
>     
>     <div>
>       <span class="Apple-style-span">Report Status: 0</span>
>     </div>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Â </span>
>   </div>
>   
>   <div>
>     <span class="Apple-style-span">Backup did not complete successfully because a shadow copy could not be created. Free up disk space on the drive that you are backing up by deleting unnecessary files and then try again.</span>
>   </div>
> </div>
> 
> <div>
>
> </div>

<div>
</div>

<div>
  And what does it all mean? Well, I just recently installed a new hard disk and installed an alternative OS onto it. This new hard disk is appearing as &#8220;Disk 0&#8221; in Disk Management and it *is* the boot device. When I boot off it and then select my Windows partition I get these error messages. It appears VSS attempts to access/lock the drive that booted the OS and it fails. If I attempt to take &#8220;Disk 0&#8221; offline, I get the following error message:
</div>

<div>
  <div>
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  </div>
  
  <div>
    Virtual Disk Manager
  </div>
  
  <div>
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  </div>
  
  <div>
    Disk attributes may not be changed on the current system disk or BIOS disk 0.
  </div>
  
  <div>
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  </div>
  
  <div>
    OK
  </div>
  
  <div>
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  </div>
</div>

<div>
</div>

<div>
  Using Procmon I can see that VSSVC.exe attempts to access a filesystem that it cannot&#8230; Well, the only disk that it can&#8217;t access is the lone &#8220;Alternative OS&#8221; disk. I suspect removing that disk or forcing my BIOS to boot directly to the Windows partition will resolve my issues. If you&#8217;re in a similar situation as me, I would suggest checking your boot order, removing any extraneous disks or ensuring your boot drive is appearing as &#8220;Disk 0&#8221; in disk management.
</div>

<div>
</div>

<div>
  I&#8217;ve just tested and confirmed that forcing my BIOS to boot directly to my OS drive without going through an alternative drive has enabled the backup program to operate without any errors.
</div>

<div>
</div>

<div>
  This blogpost is for anyone else that my experience a similar issue.
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->