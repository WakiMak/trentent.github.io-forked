---
id: 2046
title: 'Citrix Workspace Environment Manager &#8211; First Impressions'
date: 2017-03-02T14:24:55-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2017/03/02/2025-revision-v1/
permalink: /2017/03/02/2025-revision-v1/
---
Citrix Workspace Environment Manager can be used as a replacement for Active Directory (AD) Group Policy Preferences (GPP).Â It does not deal with machine policies, however.Â Because of this AD Group Policy Objects (GPO) are still required to apply policies to machines.Â However, WEMâ€™s goal isnâ€™t to manipulate machine policies but to improve user logon times by replacing the user policy of an AD GPO.Â A GPO has two different engines to apply settings.Â A Registry Policy engine and the engine that drives â€œClient Side Extensionsâ€ (CSE).Â The biggest time consumer of a GPO is processing the logic of a CSE or the action of the CSE. Â I&#8217;llÂ look at each engine and what they mean for WEM.

## Registry Extension

The first is the â€˜Registryâ€™ policy engine.Â This engine is confusingly called the â€œRegistry Extensionâ€ as opposed to the CSE â€œGroup Policy Registryâ€.Â The â€œRegistry Extensionâ€ is engine applies registry settings set in via â€˜Administrative Templatesâ€™.

<img class="aligncenter size-full wp-image-2026" src="http://theorypc.ca/wp-content/uploads/2017/03/admin_Template.png" alt="" width="694" height="260" srcset="http://theorypc.ca/wp-content/uploads/2017/03/admin_Template.png 694w, http://theorypc.ca/wp-content/uploads/2017/03/admin_Template-300x112.png 300w" sizes="(max-width: 694px) 100vw, 694px" /> 

These settings are â€˜dumbâ€™ in that there is no logic processing required.Â When set to Enabled or Disabled whatever key needs to be set with that value is applied immediately.Â Processing of this engine occurs <u>very, very fast</u> so migrating these policy settings would have minimal or no improvement to logon times (unless you have a ton of GPO&#8217;s apply and network latency becomes your primary blocker).

<img class="aligncenter size-full wp-image-2027" src="http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension.png" alt="" width="582" height="395" srcset="http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension.png 582w, http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension-300x204.png 300w" sizes="(max-width: 582px) 100vw, 582px" /> 

<img class="aligncenter size-large wp-image-2028" src="http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension_time.png" alt="" width="554" height="280" srcset="http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension_time.png 554w, http://theorypc.ca/wp-content/uploads/2017/03/RegistryExtension_time-300x152.png 300w" sizes="(max-width: 554px) 100vw, 554px" /> 

If you use [ControlUp](http://www.controlup.com) to Analyze GPO Extension Load Times it will display the Registry Extension and the Group Policy Registry CSE:

<img class="aligncenter size-full wp-image-2029" src="http://theorypc.ca/wp-content/uploads/2017/03/controlUp_Analyze.png" alt="" width="917" height="369" srcset="http://theorypc.ca/wp-content/uploads/2017/03/controlUp_Analyze.png 917w, http://theorypc.ca/wp-content/uploads/2017/03/controlUp_Analyze-300x121.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/controlUp_Analyze-768x309.png 768w" sizes="(max-width: 917px) 100vw, 917px" /> 

## 

## Client Side Extensions

However, CSEâ€™s allow you to put complex logic and actions within that require processing to determine if a setting should be applied or how a settings should be applied.Â One of the most powerful of these is the Registry CSE.Â This CSE allows you to apply registry settings with Boolean logic and can be filtered on a huge number of variables.

<img class="aligncenter size-full wp-image-2030" src="http://theorypc.ca/wp-content/uploads/2017/03/cse1.png" alt="" width="1245" height="969" srcset="http://theorypc.ca/wp-content/uploads/2017/03/cse1.png 1245w, http://theorypc.ca/wp-content/uploads/2017/03/cse1-300x233.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/cse1-768x598.png 768w" sizes="(max-width: 1245px) 100vw, 1245px" /> 

<img class="aligncenter size-large wp-image-2031" src="http://theorypc.ca/wp-content/uploads/2017/03/cse2.png" alt="" width="1015" height="469" srcset="http://theorypc.ca/wp-content/uploads/2017/03/cse2.png 1015w, http://theorypc.ca/wp-content/uploads/2017/03/cse2-300x139.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/cse2-768x355.png 768w" sizes="(max-width: 1015px) 100vw, 1015px" /> 

All of this logic is stored in a XML document that is pulled when the group policy object is processed.Â This file is located in â€œC:\ProgramData\Microsoft\Group Policy\History\_GUID OF GPO_\_SID OF USER_\Preferences\Registryâ€.

<img class="aligncenter size-full wp-image-2032" src="http://theorypc.ca/wp-content/uploads/2017/03/cse3.png" alt="" width="1400" height="765" srcset="http://theorypc.ca/wp-content/uploads/2017/03/cse3.png 1400w, http://theorypc.ca/wp-content/uploads/2017/03/cse3-300x164.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/cse3-768x420.png 768w" sizes="(max-width: 1400px) 100vw, 1400px" /> 

Parsing and executing the Boolean logic takes time.Â This is where we would be hopeful that WEM can make this faster.Â The processing of all this, in our existing environment consumes the majority of our logon time:

<img class="aligncenter size-large wp-image-2033" src="http://theorypc.ca/wp-content/uploads/2017/03/cse4.png" alt="" width="945" height="376" srcset="http://theorypc.ca/wp-content/uploads/2017/03/cse4.png 945w, http://theorypc.ca/wp-content/uploads/2017/03/cse4-300x119.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/cse4-768x306.png 768w" sizes="(max-width: 945px) 100vw, 945px" /> 

# Migrating Group Policy Preferences to WEM

Looking at some of our Registry Preferences weâ€™ll look at what is required to migrate it into WEM.  
<img class="aligncenter size-large wp-image-2035" src="http://theorypc.ca/wp-content/uploads/2017/03/reg_cse.png" alt="" width="302" height="205" srcset="http://theorypc.ca/wp-content/uploads/2017/03/reg_cse.png 302w, http://theorypc.ca/wp-content/uploads/2017/03/reg_cse-300x204.png 300w" sizes="(max-width: 302px) 100vw, 302px" /> 

## Basic settings â€œeg, â€˜Always appliedâ€™â€.

â€œVisual Effectsâ€

<img class="aligncenter size-full wp-image-2034" src="http://theorypc.ca/wp-content/uploads/2017/03/reg_cse2.png" alt="" width="1025" height="161" srcset="http://theorypc.ca/wp-content/uploads/2017/03/reg_cse2.png 1025w, http://theorypc.ca/wp-content/uploads/2017/03/reg_cse2-300x47.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/reg_cse2-768x121.png 768w" sizes="(max-width: 1025px) 100vw, 1025px" /> 

These settings have no filters and are applied to all users.Â To migrate them to WEM Iâ€™ve exported these values and set them into a registry file:

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

Switching to WEM I select â€˜Actionsâ€™ then â€˜Registry Entriesâ€™ and then I imported the registry file.

<img class="aligncenter size-full wp-image-2036" src="http://theorypc.ca/wp-content/uploads/2017/03/wem1.png" alt="" width="767" height="614" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem1.png 767w, http://theorypc.ca/wp-content/uploads/2017/03/wem1-300x240.png 300w" sizes="(max-width: 767px) 100vw, 767px" /> 

<img class="aligncenter size-large wp-image-2037" src="http://theorypc.ca/wp-content/uploads/2017/03/wem2.png" alt="" width="747" height="489" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem2.png 747w, http://theorypc.ca/wp-content/uploads/2017/03/wem2-300x196.png 300w" sizes="(max-width: 747px) 100vw, 747px" /> 

An interesting side note, it appears the import excluded the REG\_BINARY.Â However you can create the REG\_BINARY via the GUI:

<img class="aligncenter size-full wp-image-2038" src="http://theorypc.ca/wp-content/uploads/2017/03/wem3.png" alt="" width="432" height="633" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem3.png 432w, http://theorypc.ca/wp-content/uploads/2017/03/wem3-205x300.png 205w" sizes="(max-width: 432px) 100vw, 432px" /> 

To set the Registry Entries I created a filter condition called â€œAlways Trueâ€

<img class="aligncenter size-full wp-image-2039" src="http://theorypc.ca/wp-content/uploads/2017/03/wem4.png" alt="" width="1386" height="683" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem4.png 1386w, http://theorypc.ca/wp-content/uploads/2017/03/wem4-300x148.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem4-768x378.png 768w" sizes="(max-width: 1386px) 100vw, 1386px" /> 

And then created a rule â€œAlways Trueâ€

<img class="aligncenter size-large wp-image-2040" src="http://theorypc.ca/wp-content/uploads/2017/03/wem5.png" alt="" width="1140" height="588" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem5.png 1393w, http://theorypc.ca/wp-content/uploads/2017/03/wem5-300x155.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem5-768x396.png 768w" sizes="(max-width: 1140px) 100vw, 1140px" /> 

We have a user group that encompasses all of our Citrix users upon which I added in â€˜Configure Usersâ€™.Â Then, during the assignment of the registry keys I selected the â€˜Always Trueâ€™ filter:

<img class="aligncenter size-full wp-image-2041" src="http://theorypc.ca/wp-content/uploads/2017/03/wem6.png" alt="" width="1134" height="649" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem6.png 1134w, http://theorypc.ca/wp-content/uploads/2017/03/wem6-300x172.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem6-768x440.png 768w" sizes="(max-width: 1134px) 100vw, 1134px" /> 

<img class="aligncenter size-full wp-image-2042" src="http://theorypc.ca/wp-content/uploads/2017/03/wem7.png" alt="" width="285" height="396" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem7.png 285w, http://theorypc.ca/wp-content/uploads/2017/03/wem7-216x300.png 216w" sizes="(max-width: 285px) 100vw, 285px" /> 

And now these registry keys have been migrated to WEM.Â It would be nice to â€˜Groupâ€™ these keys like you can do for a collection in Group Policy Preferences.Â Without the ability to do so the name of the action becomes really important as itâ€™s the only way you can filter:

<img class="aligncenter size-full wp-image-2043" src="http://theorypc.ca/wp-content/uploads/2017/03/wem9.png" alt="" width="881" height="300" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem9.png 881w, http://theorypc.ca/wp-content/uploads/2017/03/wem9-300x102.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem9-768x262.png 768w" sizes="(max-width: 881px) 100vw, 881px" /> 

<img class="aligncenter size-large wp-image-2044" src="http://theorypc.ca/wp-content/uploads/2017/03/wem8.png" alt="" width="870" height="329" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem8.png 870w, http://theorypc.ca/wp-content/uploads/2017/03/wem8-300x113.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem8-768x290.png 768w" sizes="(max-width: 870px) 100vw, 870px" /> 

Next I&#8217;ll look at replacing Group Policy Preferences that contain some boolean logic.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->