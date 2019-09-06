---
id: 712
title: Windows Backup Error 0x81000019
date: 2010-12-12T15:13:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2010/12/12/windows-backup-error-0x81000019/
permalink: /2010/12/12/windows-backup-error-0x81000019/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2010/12/windows-backup-error-0x81000019.html
blogger_internal:
  - /feeds/7977773/posts/default/5527825523689104091
categories:
  - Blog
  - Uncategorized
tags:
  - 2008R2
---
Recently, I've been getting an error from Windows Backup:

Error Code: 0x81000019

Event Viewer lists the following additional information:
Shadow copy creation failed because of error reported by ASR Writer. More info: The requested system device cannot be found. (0x80073BC3).

Volume Shadow Copy Service warning: ASR writer Error 0x80073bc3. hr = 0x00000000, The operation completed successfully.
.

Operation:
PrepareForBackup event

Context:
Execution Context: ASR Writer
Execution Context: Writer
Writer Class Id: {be000cbe-11fe-4426-9c58-531aa6355fc4}
Writer Name: ASR Writer
Writer Instance ID: {5c8b67a8-a665-45e5-9f5c-45382f136693}

Error-specific details:
ASR Writer: The requested system device cannot be found. (0x80073BC3)

Volume Shadow Copy Service error: Unexpected error calling routine Check OnIdentifyError. hr = 0x80073bc3, The requested system device cannot be found.
.

Operation:
PrepareForBackup event

Context:
Execution Context: ASR Writer
Execution Context: Writer
Writer Class Id: {be000cbe-11fe-4426-9c58-531aa6355fc4}
Writer Name: ASR Writer
Writer Instance ID: {5c8b67a8-a665-45e5-9f5c-45382f136693}

Error-specific details:
ASR Writer: The requested system device cannot be found. (0x80073BC3)

Fault bucket 668258104, type 5
Event Name: WindowsBackupFailure
Response: Not available
Cab Id: 0

Problem signature:
P1: Backup
P2: 6.1.7600
P3: 0x81000019
P4: 7
P5:
P6:
P7:
P8:
P9:
P10:

Attached files:
C:\Windows\Logs\WindowsBackup\WindowsBackup.1.etl

These files may be available here:
C:\ProgramData\Microsoft\Windows\WER\ReportArchive\NonCritical_Backup_7a9178ddfcd376c581a8653b09ae5e2464735bf_100ef6dc

Analysis symbol:
Rechecking for solution: 0
Report Id: 8b57e781-0633-11e0-a042-90e6ba2d22c8
Report Status: 0

Backup did not complete successfully because a shadow copy could not be created. Free up disk space on the drive that you are backing up by deleting unnecessary files and then try again.


And what does it all mean? Well, I just recently installed a new hard disk and installed an alternative OS onto it. This new hard disk is appearing as "Disk 0" in Disk Management and it *is* the boot device. When I boot off it and then select my Windows partition I get these error messages. It appears VSS attempts to access/lock the drive that booted the OS and it fails. If I attempt to take "Disk 0" offline, I get the following error message:
---------------------------
Virtual Disk Manager
---------------------------
Disk attributes may not be changed on the current system disk or BIOS disk 0.
---------------------------
OK
---------------------------

Using Procmon I can see that VSSVC.exe attempts to access a filesystem that it cannot... Well, the only disk that it can't access is the lone "Alternative OS" disk. I suspect removing that disk or forcing my BIOS to boot directly to the Windows partition will resolve my issues. If you're in a similar situation as me, I would suggest checking your boot order, removing any extraneous disks or ensuring your boot drive is appearing as "Disk 0" in disk management.

I've just tested and confirmed that forcing my BIOS to boot directly to my OS drive without going through an alternative drive has enabled the backup program to operate without any errors.

This blogpost is for anyone else that my experience a similar issue.
