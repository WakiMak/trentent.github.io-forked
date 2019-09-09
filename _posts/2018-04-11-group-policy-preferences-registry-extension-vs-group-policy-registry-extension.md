---
id: 2726
title: Group Policy Preferences Registry Extension vs Group Policy Registry Extension
date: 2018-04-11T00:15:31-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2726
permalink: /2018/04/11/group-policy-preferences-registry-extension-vs-group-policy-registry-extension/
image: /wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.26.21-AM.png
categories:
  - Blog
tags:
  - Group Policy
  - Performance
---
In various discussions I've read about the drawbacks of Group Policy Preferences but is it really that bad?

&nbsp;

...Or is it how you are using it?

&nbsp;

There are two methods of applying registry keys/values with Group Policy. Â The Group Policy Registry Extension is the "traditional" form of applying policies. Â Also known as ADM or ADMX policies, when creating GPO's with this method a binary file, ".pol", is created. Â When policy application occurs this file is read and applied to your registry. Â As a binary file, this file is kept small and fast. Â Reading and applying the settings should be nearly instant.

The second method of applying registry keys is with Group Policy Preferences (GPP). Â This was a ["new" method introduced in Windows Server 2008](https://blogs.technet.microsoft.com/askds/2007/11/28/introducing-group-policy-preferences/) with the purchase of PolicyMaker by Microsoft. Â Group Policy Preferences are much, much more flexible than the traditional form. Â There are different ways of applying registry values, including the [CRUD model (Create, Replace, Update, Delete),](https://blogs.msdn.microsoft.com/microsoft_press/2009/09/29/william-stanek-applying-group-policy-preferences-with-crud/) filtering by the way of "[Item Level Targeting](http://www.dell.com/support/article/us/en/04/sln285439/windows-server-using-item-level-targeting-with-group-policy-preferences?lang=en)", either on an individual value or on a collection.

I've seen an organization heavily leverage GPP to great success. Â I started to wonder though, what are the performance impacts of using GPP over the traditional method. Â This post will explore the differences in the CRUD model and how it compares to the traditional method..

I intend to look the following scenarios:

  1. Creating a registry value
  2. Updating a previous registry value
  3. Removing a registry value

However, GPP has a fourth method, "Replace" and I'll explore what it does in addition to these 3 methods.

## Creating a Registry Value

In this scenario, the registry will be clean and a new value will be created. Â I'm going to refer to the Group Policy Registry Extension (AKA, Administrative Templates, ADM/ADX) as the "traditional" method and use the abbreviation GPP for the Group Policy <span style="text-decoration: underline;"><strong>Preferences</strong></span> Registry Extension.

### Traditional:

<img class="aligncenter size-full wp-image-2728" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.14.41-PM.png" alt="" width="1316" height="172" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.14.41-PM.png 1316w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.14.41-PM-300x39.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.14.41-PM-768x100.png 768w" sizes="(max-width: 1316px) 100vw, 1316px" /> 

After reading the Registry.Pol from the sysvol, the application of the registry key takes just 3 operations. Â RegCreateKey, RegSetValue, and RegCloseKey.

<img class="aligncenter size-full wp-image-2727" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.15.01-PM.png" alt="" width="366" height="44" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.15.01-PM.png 366w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.15.01-PM-300x36.png 300w" sizes="(max-width: 366px) 100vw, 366px" /> 

Each one of these operations took around 1-1.1ms, with the caveat that Process Monitor (procmon) consumes some resources capturing this information, slowing it down slightly.

&nbsp;

### GPP:

<img class="aligncenter size-full wp-image-2729" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.24.19-PM.png" alt="" width="1312" height="172" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.24.19-PM.png 1312w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.24.19-PM-300x39.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.24.19-PM-768x101.png 768w" sizes="(max-width: 1312px) 100vw, 1312px" /> 

We can see a new operation "RegQueryValue". Â As [described by William Stanek](https://blogs.msdn.microsoft.com/microsoft_press/2009/09/29/william-stanek-applying-group-policy-preferences-with-crud/), "The Create action creates a preference if it doesn't already exist. For example, you can use the Create action to create and set the value of a user environment variable called _CurrentOrg_ on computers where it does not yet exist. _<span style="text-decoration: underline;"><strong>If the variable already exists</strong></span>_, the value of the variable will not be changed."

The RegQueryValue is executing the check to see if a variable already exists. Â So what does GPP look like if the value is already present?

<img class="aligncenter size-full wp-image-2730" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.33.04-PM.png" alt="" width="1316" height="115" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.33.04-PM.png 1316w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.33.04-PM-300x26.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.33.04-PM-768x67.png 768w" sizes="(max-width: 1316px) 100vw, 1316px" /> 

3 operations with the process exiting on a success on the value being present.

The end result, is 3 operations for our traditional method, and 4 operations for the Group Policy Preferences method for creating a registry entry.

## Updating a registry value

In this scenario, the registry will contain a value, and the policy will be updated with a new value. Â For the traditional method this will involve changing the Microsoft "User Profiles" policy. Â I set the "HomeDir" location to "TrententTest", applied the value, then updated it to "TrententTye". Â This will ensure a new, changed key is applied. Â For GPP I'm going to change the value on the policy to 0x0 from 0x1 and use the "Update" operation.

### Traditional:

<img class="aligncenter size-full wp-image-2732" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.45.04-PM.png" alt="" width="1324" height="365" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.45.04-PM.png 1324w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.45.04-PM-300x83.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.45.04-PM-768x212.png 768w" sizes="(max-width: 1324px) 100vw, 1324px" /> 

Traditional maintains a very simple "3 operation" action with updating a value having the same effect as if the value was never present to begin with.

### GPP:

<img class="aligncenter size-full wp-image-2731" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.38.10-PM.png" alt="" width="1308" height="115" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.38.10-PM.png 1308w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.38.10-PM-300x26.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.38.10-PM-768x68.png 768w" sizes="(max-width: 1308px) 100vw, 1308px" /> 

With the "Update" action, GPP now executes just 3 operations, same as the traditional.

The end result, is 3 operations for our traditional method, and 3 operations for the Group Policy Preferences method for updating a registry entry.

## Removing a registry value

In this scenario, I am going to remove a registry value. Â Using the traditional method this means modifying my group policy to "Not Configured", and for GPP this means setting "Delete" for our operation.

### Traditional:

<img class="aligncenter size-full wp-image-2733" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.02.24-AM.png" alt="" width="1109" height="106" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.02.24-AM.png 1109w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.02.24-AM-300x29.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.02.24-AM-768x73.png 768w" sizes="(max-width: 1109px) 100vw, 1109px" /> 

Again, Traditional performs it's work in just 3 operations.

&nbsp;

### GPP:

<img class="aligncenter size-full wp-image-2734" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.57.07-PM.png" alt="" width="1316" height="113" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.57.07-PM.png 1316w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.57.07-PM-300x26.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-11.57.07-PM-768x66.png 768w" sizes="(max-width: 1316px) 100vw, 1316px" /> 

GPP also performs this work in just 3 operations.

&nbsp;

## GPP - The Replace Method

Group Policy Preferences has another operation to explore. Â "Replace".

This operationÂ ..."creates preferences that don't yet exist, or deletes and then creates preferences that already exist."

This sounds like it performs a few operations. Â Lets see what it looks like:

<img class="aligncenter size-full wp-image-2735" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.07.41-AM.png" alt="" width="1317" height="139" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.07.41-AM.png 1317w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.07.41-AM-300x32.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.07.41-AM-768x81.png 768w" sizes="(max-width: 1317px) 100vw, 1317px" /> 

Replace executes "6" operations. Â RegOpenKey, RegDeleteValue, RegCloseKey, RegCreateKey, RegSetValue, RegCloseKey. Â I'm not entirely sure why you'd want a DeleteValue before SetValue but that's what this selection does.

&nbsp;

## Revisiting GPP: "Creating a Registry Value"

During the process of creating this post, I wondered if the 3 operation "Update" would work better for creating a key. Â The GPP "Create" selection has 4 operations, but the "Update" selection only has 3 operations. Â I deleted my "TrententTestPreferences" key and refreshed group policy:

<img class="aligncenter size-full wp-image-2736" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.14.05-AM.png" alt="" width="1211" height="113" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.14.05-AM.png 1211w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.14.05-AM-300x28.png 300w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.14.05-AM-768x72.png 768w" sizes="(max-width: 1211px) 100vw, 1211px" /> 

&nbsp;

3 operations! Â So Group Policy Preferences has the potential to operate at the same speed as the traditional group policy _IF YOU STICK TO USING "UPDATE"_. Â At the very least, these operations should take the same amount of time. Â Of course, implementation might be a different story.

The final tally:

<img class="aligncenter size-full wp-image-2737" src="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.24.34-AM.png" alt="" width="536" height="240" srcset="http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.24.34-AM.png 536w, http://theorypc.ca/wp-content/uploads/2018/04/Screen-Shot-2018-04-10-at-12.24.34-AM-300x134.png 300w" sizes="(max-width: 536px) 100vw, 536px" /> 

Stay tuned for part 2 - The Performance Comparison

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->