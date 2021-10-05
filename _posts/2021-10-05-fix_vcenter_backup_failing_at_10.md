---
id: 2899
title: Fix vCenter backup failing at 10%
date: 2021-10-05T11:46:00-06:00
author: trententtye
excerpt: BackupManager encountered an exception
layout: post
permalink: /2021/06/05/fix_vcenter_backup_failing_at_10/
image: 
categories:
  - Blog
tags:
  - vCenter
  - VMware
  - 7.0
---

I was trying to backup my machine and got the following error(s):
"BackupManager encountered an exception"

This was right at the 10% mark of the backup process.  When I tried to manually run the backup I got the same error.  I changed the storage, same error.

When I was looking through the /var/log/vmware/applmgmt/backup.log I found lines like the following:
"getDbHealthStatus returns status as DEGRADED"
"VCDB"

[The fix was found here:](https://richdowling.wordpress.com/2021/06/24/vcenter-7-exception-full-backup-not-allowed-during-vm-snapshot/)

Simply remove or rename /etc/vmware/backupMarker.txt and re-run the backup.

And my backups started working after that!
