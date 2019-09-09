---
id: 2025
title: Citrix Workspace Environment Manager - First Impressions
date: 2017-03-02T14:25:07-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2025
permalink: /2017/03/02/citrix-workspace-environment-manager-first-impressions/
image: /wp-content/uploads/2017/03/wem_icon.png
categories:
  - Blog
tags:
  - Citrix
  - Group Policy
  - Performance
  - Registry
  - Workspace Environment Manager
---
Citrix Workspace Environment Manager can be used as a replacement for Active Directory (AD) Group Policy Preferences (GPP). It does not deal with machine policies, however. Because of this AD Group Policy Objects (GPO) are still required to apply policies to machines. However, WEM"s goal isn"t to manipulate machine policies but to improve user logon times by replacing the user policy of an AD GPO. A GPO has two different engines to apply settings. A Registry Policy engine and the engine that drives Client Side Extensions(CSE). The biggest time consumer of a GPO is processing the logic of a CSE or the action of the CSE.  I'll look at each engine and what they mean for WEM.

## Registry Extension

The first is the "Registry" policy engine. This engine is confusingly called the Registry Extension as opposed to the CSE Group Policy Registry. The Registry Extension is engine applies registry settings set in via "Administrative Templates".

<img class="aligncenter size-full wp-image-2026" src="/wp-content/uploads/2017/03/admin_Template.png" alt="" width="694" height="260" srcset="/wp-content/uploads/2017/03/admin_Template.png 694w, /wp-content/uploads/2017/03/admin_Template-300x112.png 300w" sizes="(max-width: 694px) 100vw, 694px" /> 

These settings are "dumb" in that there is no logic processing required. When set to Enabled or Disabled whatever key needs to be set with that value is applied immediately. Processing of this engine occurs <u>very, very fast</u> so migrating these policy settings would have minimal or no improvement to logon times (unless you have a ton of GPO's apply and network latency becomes your primary blocker).

<img class="aligncenter size-full wp-image-2027" src="/wp-content/uploads/2017/03/RegistryExtension.png" alt="" width="582" height="395" srcset="/wp-content/uploads/2017/03/RegistryExtension.png 582w, /wp-content/uploads/2017/03/RegistryExtension-300x204.png 300w" sizes="(max-width: 582px) 100vw, 582px" /> 

<img class="aligncenter size-large wp-image-2028" src="/wp-content/uploads/2017/03/RegistryExtension_time.png" alt="" width="554" height="280" srcset="/wp-content/uploads/2017/03/RegistryExtension_time.png 554w, /wp-content/uploads/2017/03/RegistryExtension_time-300x152.png 300w" sizes="(max-width: 554px) 100vw, 554px" /> 

If you use [ControlUp](http://www.controlup.com) to Analyze GPO Extension Load Times it will display the Registry Extension and the Group Policy Registry CSE:

<img class="aligncenter size-full wp-image-2029" src="/wp-content/uploads/2017/03/controlUp_Analyze.png" alt="" width="917" height="369" srcset="/wp-content/uploads/2017/03/controlUp_Analyze.png 917w, /wp-content/uploads/2017/03/controlUp_Analyze-300x121.png 300w, /wp-content/uploads/2017/03/controlUp_Analyze-768x309.png 768w" sizes="(max-width: 917px) 100vw, 917px" /> 

## 

## Client Side Extensions

However, CSE"s allow you to put complex logic and actions within that require processing to determine if a setting should be applied or how a settings should be applied. One of the most powerful of these is the Registry CSE. This CSE allows you to apply registry settings with Boolean logic and can be filtered on a huge number of variables.

<img class="aligncenter size-full wp-image-2030" src="/wp-content/uploads/2017/03/cse1.png" alt="" width="1245" height="969" srcset="/wp-content/uploads/2017/03/cse1.png 1245w, /wp-content/uploads/2017/03/cse1-300x233.png 300w, /wp-content/uploads/2017/03/cse1-768x598.png 768w" sizes="(max-width: 1245px) 100vw, 1245px" /> 

<img class="aligncenter size-large wp-image-2031" src="/wp-content/uploads/2017/03/cse2.png" alt="" width="1015" height="469" srcset="/wp-content/uploads/2017/03/cse2.png 1015w, /wp-content/uploads/2017/03/cse2-300x139.png 300w, /wp-content/uploads/2017/03/cse2-768x355.png 768w" sizes="(max-width: 1015px) 100vw, 1015px" /> 

All of this logic is stored in a XML document that is pulled when the group policy object is processed. This file is located in C:\ProgramData\Microsoft\Group Policy\History\_GUID OF GPO_\_SID OF USER_\Preferences\Registry.

<img class="aligncenter size-full wp-image-2032" src="/wp-content/uploads/2017/03/cse3.png" alt="" width="1400" height="765" srcset="/wp-content/uploads/2017/03/cse3.png 1400w, /wp-content/uploads/2017/03/cse3-300x164.png 300w, /wp-content/uploads/2017/03/cse3-768x420.png 768w" sizes="(max-width: 1400px) 100vw, 1400px" /> 

Parsing and executing the Boolean logic takes time. This is where we would be hopeful that WEM can make this faster. The processing of all this, in our existing environment consumes the majority of our logon time:

<img class="aligncenter size-large wp-image-2033" src="/wp-content/uploads/2017/03/cse4.png" alt="" width="945" height="376" srcset="/wp-content/uploads/2017/03/cse4.png 945w, /wp-content/uploads/2017/03/cse4-300x119.png 300w, /wp-content/uploads/2017/03/cse4-768x306.png 768w" sizes="(max-width: 945px) 100vw, 945px" /> 

# Migrating Group Policy Preferences to WEM

Looking at some of our Registry Preferences we"ll look at what is required to migrate it into WEM.  
<img class="aligncenter size-large wp-image-2035" src="/wp-content/uploads/2017/03/reg_cse.png" alt="" width="302" height="205" srcset="/wp-content/uploads/2017/03/reg_cse.png 302w, /wp-content/uploads/2017/03/reg_cse-300x204.png 300w" sizes="(max-width: 302px) 100vw, 302px" /> 

## Basic settings eg, "Always applied".

Visual Effects 

<img class="aligncenter size-full wp-image-2034" src="/wp-content/uploads/2017/03/reg_cse2.png" alt="" width="1025" height="161" srcset="/wp-content/uploads/2017/03/reg_cse2.png 1025w, /wp-content/uploads/2017/03/reg_cse2-300x47.png 300w, /wp-content/uploads/2017/03/reg_cse2-768x121.png 768w" sizes="(max-width: 1025px) 100vw, 1025px" /> 

These settings have no filters and are applied to all users. To migrate them to WEM I"ve exported these values and set them into a registry file:

<pre class="lang:reg decode:true ">Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Control Panel\Desktop]
"FontSmoothing"=dword:00000000
"UserPreferencesMask"=hex:90,12,01,80,10,00,00,00

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced]
"ListviewAlphaSelect"=dword:00000000
"TaskbarAnimations"=dword:00000000
"ListviewWatermark"=dword:00000000
"ListviewShadow"=dword:00000000

[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects]
"VisualFXSetting"=dword:00000003

[HKEY_CURRENT_USER\Control Panel\Desktop\WindowMetrics]
"MinAnimate"=dword:00000000
</pre>

Switching to WEM I select "Actions" then "Registry Entries" and then I imported the registry file.

<img class="aligncenter size-full wp-image-2036" src="/wp-content/uploads/2017/03/wem1.png" alt="" width="767" height="614" srcset="/wp-content/uploads/2017/03/wem1.png 767w, /wp-content/uploads/2017/03/wem1-300x240.png 300w" sizes="(max-width: 767px) 100vw, 767px" /> 

<img class="aligncenter size-large wp-image-2037" src="/wp-content/uploads/2017/03/wem2.png" alt="" width="747" height="489" srcset="/wp-content/uploads/2017/03/wem2.png 747w, /wp-content/uploads/2017/03/wem2-300x196.png 300w" sizes="(max-width: 747px) 100vw, 747px" /> 

An interesting side note, it appears the import excluded the REG\_BINARY. However you can create the REG\_BINARY via the GUI:

<img class="aligncenter size-full wp-image-2038" src="/wp-content/uploads/2017/03/wem3.png" alt="" width="432" height="633" srcset="/wp-content/uploads/2017/03/wem3.png 432w, /wp-content/uploads/2017/03/wem3-205x300.png 205w" sizes="(max-width: 432px) 100vw, 432px" /> 

To set the Registry Entries I created a filter condition called Always True 

<img class="aligncenter size-full wp-image-2039" src="/wp-content/uploads/2017/03/wem4.png" alt="" width="1386" height="683" srcset="/wp-content/uploads/2017/03/wem4.png 1386w, /wp-content/uploads/2017/03/wem4-300x148.png 300w, /wp-content/uploads/2017/03/wem4-768x378.png 768w" sizes="(max-width: 1386px) 100vw, 1386px" /> 

And then created a rule Always True 

<img class="aligncenter size-large wp-image-2040" src="/wp-content/uploads/2017/03/wem5.png" alt="" width="1140" height="588" srcset="/wp-content/uploads/2017/03/wem5.png 1393w, /wp-content/uploads/2017/03/wem5-300x155.png 300w, /wp-content/uploads/2017/03/wem5-768x396.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

We have a user group that encompasses all of our Citrix users upon which I added in "Configure Users". Then, during the assignment of the registry keys I selected the "Always True" filter:

<img class="aligncenter size-full wp-image-2041" src="/wp-content/uploads/2017/03/wem6.png" alt="" width="1134" height="649" srcset="/wp-content/uploads/2017/03/wem6.png 1134w, /wp-content/uploads/2017/03/wem6-300x172.png 300w, /wp-content/uploads/2017/03/wem6-768x440.png 768w" sizes="(max-width: 1134px) 100vw, 1134px" /> 

<img class="aligncenter size-full wp-image-2042" src="/wp-content/uploads/2017/03/wem7.png" alt="" width="285" height="396" srcset="/wp-content/uploads/2017/03/wem7.png 285w, /wp-content/uploads/2017/03/wem7-216x300.png 216w" sizes="(max-width: 285px) 100vw, 285px" /> 

And now these registry keys have been migrated to WEM. It would be nice to "Group" these keys like you can do for a collection in Group Policy Preferences. Without the ability to do so the name of the action becomes really important as it"s the only way you can filter:

<img class="aligncenter size-full wp-image-2043" src="/wp-content/uploads/2017/03/wem9.png" alt="" width="881" height="300" srcset="/wp-content/uploads/2017/03/wem9.png 881w, /wp-content/uploads/2017/03/wem9-300x102.png 300w, /wp-content/uploads/2017/03/wem9-768x262.png 768w" sizes="(max-width: 881px) 100vw, 881px" /> 

<img class="aligncenter size-large wp-image-2044" src="/wp-content/uploads/2017/03/wem8.png" alt="" width="870" height="329" srcset="/wp-content/uploads/2017/03/wem8.png 870w, /wp-content/uploads/2017/03/wem8-300x113.png 300w, /wp-content/uploads/2017/03/wem8-768x290.png 768w" sizes="(max-width: 870px) 100vw, 870px" /> 

Next I'll look at replacing Group Policy Preferences that contain some boolean logic.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
