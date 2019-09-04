---
id: 576
title: 'APPV5 &#8211; Virtualization Template'
date: 2015-02-20T14:06:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/02/20/appv5-virtualization-template/
permalink: /2015/02/20/appv5-virtualization-template/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/02/appv5-virtualization-template.html
blogger_internal:
  - /feeds/7977773/posts/default/9213139227093632222
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
Use if you want, or not. Â This virtualization template is to be applied against the sequencer. Â I&#8217;ve found it removes a lot of useless captured information that can get caught in a sequence.

<pre class="lang:default decode:true ">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;SequencerTemplate xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"&gt;
  &lt;AllowMU&gt;false&lt;/AllowMU&gt;
  &lt;AppendPackageVersionToFilename&gt;true&lt;/AppendPackageVersionToFilename&gt;
  &lt;AllowLocalInteractionToCom&gt;false&lt;/AllowLocalInteractionToCom&gt;
  &lt;AllowLocalInteractionToObject&gt;false&lt;/AllowLocalInteractionToObject&gt;
  &lt;FullVFSWriteMode&gt;true&lt;/FullVFSWriteMode&gt;
  &lt;ExcludePreExistingSxSAndVC&gt;false&lt;/ExcludePreExistingSxSAndVC&gt;
  &lt;FileExclusions&gt;
    &lt;string&gt;[{CryptoKeys}]&lt;/string&gt;
    &lt;string&gt;[{Common AppData}]\Microsoft\Crypto&lt;/string&gt;
    &lt;string&gt;[{Common AppData}]\Microsoft\Search\Data&lt;/string&gt;
    &lt;string&gt;[{Cookies}]&lt;/string&gt;
    &lt;string&gt;[{History}]&lt;/string&gt;
    &lt;string&gt;[{Cache}]&lt;/string&gt;
    &lt;string&gt;[{Local AppData}]&lt;/string&gt;
    &lt;string&gt;[{Personal}]&lt;/string&gt;
    &lt;string&gt;[{Profile}]\Local Settings&lt;/string&gt;
    &lt;string&gt;[{Profile}]\NTUSER.DAT&lt;/string&gt;
    &lt;string&gt;[{Profile}]\NTUSER.DAT.LOG1&lt;/string&gt;
    &lt;string&gt;[{Profile}]\NTUSER.DAT.LOG2&lt;/string&gt;
    &lt;string&gt;[{Recent}]&lt;/string&gt;
    &lt;string&gt;[{Windows}]\Installer&lt;/string&gt;
    &lt;string&gt;[{Windows}]\Debug&lt;/string&gt;
    &lt;string&gt;[{Windows}]\Logs\CBS&lt;/string&gt;
    &lt;string&gt;[{Windows}]\Temp&lt;/string&gt;
    &lt;string&gt;[{Windows}]\WinSxS\ManifestCache&lt;/string&gt;
    &lt;string&gt;[{Windows}]\WindowsUpdate.log&lt;/string&gt;
    &lt;string&gt;[{Windows}]\System32\config&lt;/string&gt;
    &lt;string&gt;[{System}]\config&lt;/string&gt;
    &lt;string&gt;[{Windows}]\ServiceProfiles&lt;/string&gt;
    &lt;string&gt;[{Windows}]\Logs&lt;/string&gt;
    &lt;string&gt;[{AppVPackageDrive}]\$Recycle.Bin&lt;/string&gt;
    &lt;string&gt;[{AppVPackageDrive}]\Boot&lt;/string&gt;
    &lt;string&gt;[{AppVPackageDrive}]\System Volume Information&lt;/string&gt;
    &lt;string&gt;[{AppVSystem32Logfiles}]\Scm&lt;/string&gt;
    &lt;string&gt;[{AppData}]\Microsoft\AppV&lt;/string&gt;
    &lt;string&gt;[{Local AppData}]\Temp&lt;/string&gt;
    &lt;string&gt;[{LocalAppDataLow}]\Microsoft\CryptnetUrlCache&lt;/string&gt;
    &lt;string&gt;[{ProgramFilesX64}]\Microsoft Application Virtualization\Sequencer&lt;/string&gt;
  &lt;/FileExclusions&gt;
  &lt;RegExclusions&gt;
    &lt;string&gt;REGISTRY\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Cryptography&lt;/string&gt;
    &lt;string&gt;REGISTRY\MACHINE\SOFTWARE\Microsoft\Cryptography&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\StreamMRU&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\StreamMRU&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\Streams&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Streams&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist&lt;/string&gt;
    &lt;string&gt;REGISTRY\MACHINE\SOFTWARE\Microsoft\AppV&lt;/string&gt;
    &lt;string&gt;REGISTRY\MACHINE\SOFTWARE\Wow6432Node\Microsoft\AppV&lt;/string&gt;
    &lt;string&gt;REGISTRY\MACHINE\SYSTEM\CurrentControlSet\Control\MUI&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\AppV&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\ResKit&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Wow6432Node\Microsoft\AppV&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\MuiCache&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\Software\Microsoft\Windows\Shell\BagMRU&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]_CLASSES\Local Settings\Software\Microsoft\Windows\Shell\Bags&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Explorer&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Internet Explorer\Toolbar&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\[{AppVCurrentUserSID}]\Software\Microsoft\RestartManager&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\.DEFAULT\SOFTWARE\Classes\Local Settings\MuiCache&lt;/string&gt;
    &lt;string&gt;REGISTRY\USER\.DEFAULT\Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap&lt;/string&gt;
  &lt;/RegExclusions&gt;
  &lt;TargetOSes /&gt;
&lt;/SequencerTemplate&gt;</pre>

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->