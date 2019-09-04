---
id: 1183
title: Count Characters in a Batch File
date: 2016-04-25T11:24:38-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/blog/2016/04/25/691-autosave-v1/
permalink: /2016/04/25/691-autosave-v1/
---
<pre class="lang:batch decode:true ">ECHO OFF
:Set some variables, the string variable, a second string variable and our starting count.
set str=\\fileservertestserver\accounting\Budgets\Budgets 2012\2012 Budget GA Loads
set str2=%str%
set N=0
setLocal EnableDELAYedExpansion

:the loop works like so... we check to see if N has been incremented to 55 (our 
:target number of characters). If it has, it goes to a new routine which creates
:our path. If it does not, we increment N by one then check to see if the string
:has reached a NULL character. If it has not reached "55" then goto loop and repeat.
:loop
if !N! equ 55 (
goto :exceedcharacterquota
)

set /A N+=1

ECHO N=!N!
if "!str:~1!" neq "" (
set str=!str:~1!
goto :loop
)

:if string length exceeds 55 chars, take the first 25 chars and the last 25 chars with an ellipse (...)
:in between.
:exceedcharacterquota
set string-part-one=!str2:~0,25!
set string-part-two=!str2:~-25!
echo !string-part-one!...!string-part-two!</pre>

I have an issue where I need to ensure I don&#8217;t exceed a certain number of characters in a script. Specifically, I cannot exceed 64 characters while making a group through script in AD. To do this I came up with the following:

&nbsp;

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->