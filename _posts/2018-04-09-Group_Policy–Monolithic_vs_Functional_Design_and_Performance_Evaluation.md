---
id: 2900
title: Group Policy – Monolithic vs Functional Design and Performance Evaluation
date: 2018-04-09T00:15:31-06:00
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

Group Policy Design is a hotly discussed topic, with lots of different ideas and discussions.  However, there is not a whole lot of actual metrics.  ADM and ADMX templates apply registry keys in an ‘enforced’ manner.  That is, if you or the machine has access to read the policies, the registry keys within are applied.  If you stuck purely to ADM/ADMX policies but wanted to do dynamic filtering or application of the keys/values based on a set of criteria you’d probably design multiple policies and nested organizational units (OUs).  From here, you could filter certain policies based on the machine or user location in the OU structure or by filtering on the policies themselves and denying access to certain groups or doing explicit allows to certain groups.  This design style, back in the day, was called "[functional](https://web.archive.org/web/20200930214355/http://www.itprotoday.com/management-mobility/group-policy-design-best-practices)" design.

<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-01-at-10.48.20-PM-418x550.png" />

However, the alternative style, “monolithic” design, simplifies the group policy object (GPO) design into much fewer GPO’s.

<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Monolthic.png" />

## Setup

My test setup is very simple; a organizational unit (OU) with inheritance blocked to control the application of the GPO’s.  I created 100 individual GPO’s with a single registry value, and 1 GPO with 100 values.  I chose to do a simple registry addition as it should be the best performance option for group policy.  I created a custom ADMX file for this purpose:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--  (c) 2018 TheoryPC  -->
<policyDefinitions xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" revision="1.0" schemaVersion="1.0" xmlns="http://schemas.microsoft.com/GroupPolicy/2006/07/PolicyDefinitions">
  <policyNamespaces>
    <target prefix="theorypc" namespace="trentent.Policies.theorypc" />
    <using prefix="windows" namespace="Microsoft.Policies.Windows" />
  </policyNamespaces>
  <resources minRequiredRevision="1.0" />
  <policies>
    <policy name="TrententTest_001" class="Machine" displayName="$(string.Value001)" explainText="$(string.explain_Help)" key="Software\Policies\TrententTest" valueName="001">
      <parentCategory ref="windows:System" />
      <supportedOn ref="windows:SUPPORTED_Win2k" />
    </policy>
    <policy name="TrententTest_002" class="Machine" displayName="$(string.Value002)" explainText="$(string.explain_Help)" key="Software\Policies\TrententTest" valueName="002">
      <parentCategory ref="windows:System" />
      <supportedOn ref="windows:SUPPORTED_Win2k" />
    </policy>
    <policy name="TrententTest_003" class="Machine" displayName="$(string.Value003)" explainText="$(string.explain_Help)" key="Software\Policies\TrententTest" valueName="003">
      <parentCategory ref="windows:System" />
      <supportedOn ref="windows:SUPPORTED_Win2k" />
    </policy>
...
```

## Monolithic simulation:
<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-9.14.16-PM.png" />

## Functional Simulation
<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-9.18.23-PM.png" />

## Testing
In testing these two designs I elected to focus on the one factor that would have the most impact: latency.  I setup my client machine in the OU, put a WAN emulator that can manipulate latency and measured the performance between the functional and monolithic designs at varying latencies.  I looked for the following event ID’s: 4257, 5257, 4016, 5016.  The x257 events correspond to when group policy downloads the group policy objects off the SYSVOL file share.  The x016 event’s determine how long it took the policy to be processed.

<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-07-at-9.12.04-PM.png" />

<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-07-at-8.48.27-PM.png" />

## The Results:
<img class="aligncenter size-full" src="/wp-content/uploads/2018/04/Screen-Shot-2018-04-09-at-9.43.54-PM.png" />

## Raw Data:

**Functional GPO – applying 100 registry values**
Event ID 4016 to 5016 (ms)

| Latency | Time (ms) | 
|-------|--------|
| 0 | 271 |
| 10 | 4089 |
| 25 | 8078 |
| 50 | 15315 |
| 75 | 22904 |
| 100 | 29820 |

Event ID 4257 to 5257 – Starting to download policies
| Latency | Time (s) | 
|-------|--------|
| 0 | 0 |
| 10 | 3 |
| 25 | 6 |
| 50 | 12 |
| 75 | 17 |
| 100 | 22 |


**Monolithic GPO – applying 100 registry values**
Event ID 4016 to 5016 (ms)	
| Latency | Time (ms) | 
|-------|--------|
| 0 | 117 |
| 10 | 156 |
| 25 | 198 |
| 50 | 284 |
| 75 | 336 |
| 100 | 435 |


Event ID 4257 to 5257 – Starting to download policies	
Latency	Time (s)
| Latency | Time (s) | 
|-------|--------|
| 0 | 0 |
| 10 | 1 |
| 25 | 1 |
| 50 | 1 |
| 75 | 1 |
| 100 | 1 |

## Analysis
There is a huge edge to the monolithic design.  Both in terms of how long it takes to process a single policy vs multiple policies and the resiliency to the effects of latency.  Even ‘light’ latency can have a profound impact on performance.  Going from 0ms to 10ms increased the length of time to process a functional design by 15 times!  The monolithic design, on the other hand, was barely impacted.  Even with a latency of 100ms, it only added ~300ms of time to process the policy.  I would consider this imperceptible in the real world, where as the functional design going from ~271ms to ~4000ms would be an extremely noticeable impact!  Let alone about 30 seconds at 100ms!

Another factor is how much additional time is required to download the policies.  This is time in addition to the processing time.  This probably shouldn’t be a huge surprise, it appears that group policies are downloaded and processed sequentially, in an order.  I’m sure this is necessary to maintain some semblance of prediction if you have conflicting policies settings, the one last in the list (whether sorted alphabetically or what have you) can be relied on to be the winner.

Adding latency, even just a little latency, has a noticeable impact.  And the more policies, the more traffic, the more the impact of latency.  Again, a loss for a functional design and a win for a more monolithic design.

## Conclusion:
Group Policy Objects can have a large impact on user experience.  The goal should be to minimize them to as few as possible.  As with everything, there are exceptions to the rules, but for Group Policy it’s important to try and maintain this rule.  Even just a little latency between the domain controller and the client can have a massive impact in group policy performance.  This can impact the length of time it takes a machine to boot, to delaying a user logging into a system.
