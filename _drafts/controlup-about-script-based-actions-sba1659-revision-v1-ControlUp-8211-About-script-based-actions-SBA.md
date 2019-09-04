---
id: 1682
title: 'ControlUp &#8211; About script-based actions (SBA)'
date: 2016-08-30T23:29:29-06:00
author: trententtye
layout: revision
guid: http://theorypc.ca/2016/08/30/1659-revision-v1/
permalink: /2016/08/30/1659-revision-v1/
---
There is a software product called &#8216;[ControlUp](http://www.controlup.com)&#8216; and it&#8217;s an immensely valuable tool forÂ _**real-timeÂ**_ monitoring, troubleshooting and reactive resolution of issues for servers, VDI, and Citrix. Â It has (very smartly) created color coded output of its various columns that serve to highlight (potential) problems with <span style="text-decoration: underline;"><strong>the very first set of predefined thresholds that require no or very little modification</strong></span>. Â Truly, this is one of the most impressive things I&#8217;ve found about it so far. Â It&#8217;s literally plug and play 95% of the time with small tweaks **that are immediate and you can visually see the impact**.Â I believe ControlUp is free up to 50 &#8216;licenses&#8217; with &#8216;license consumption&#8217; varying depending on whether you are a &#8216;session&#8217;, &#8216;server&#8217;, &#8216;workstation&#8217;, or &#8216;terminal server&#8217;.

One of the biggest features I saw and have fallen in love with is the extensiblity of the program with &#8216;Script-Based Actions&#8217; (SBA). Â Script-based actions are, simply, saved batch files/vbs/ps1 files. Â Their execution &#8216;context&#8217; is :  
<span style="text-decoration: underline;"><strong>Locally (Current User)</strong></span> &#8212; your user credentials with the script executed on the same box as the ControlUp application  
<span style="text-decoration: underline;"><strong>Prompt</strong></span> &#8212; user credentials you specifyÂ executed on the same box as the ControlUp application  
<span style="text-decoration: underline;"><strong>Local System</strong></span>Â &#8212; executes under the &#8216;Local System&#8217; context on that target computer. Â That is &#8216;SYSTEM&#8217; if you look at task manager.  
<span style="text-decoration: underline;"><strong>Session</strong></span> &#8212; This script is run in the user context which grants you the ability to execute simple commands that return results only relevant to that user without searching/filtering/verifying.

The different options also determinesÂ <span style="text-decoration: underline;"><strong>where</strong></span> the script is executed. Â **Session** andÂ **Local System** are executed on the targeted system where asÂ **Locally (Current User)Â** andÂ **Prompt** are executed right off the server where you are running the ControlUp console.

With these different execution context&#8217;s you need to think about what information or action you are looking to accomplish, what permissions are required and what rights are required if it needs to be done remotely. Â Let&#8217;s go through some examples of problems you need to solve:

  1. You want to know what groups a user belongs.
  2. You want to disable logon&#8217;s to a server.
  3. You want to refresh AppV published applications that are applied globally.

Starting with problem 1:Â **You want to know what groups a user belongs.**

<img class="aligncenter size-full wp-image-1663" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.47.46-PM.png" alt="Screen Shot 2016-08-30 at 9.47.46 PM" width="599" height="445" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.47.46-PM.png 599w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.47.46-PM-300x223.png 300w" sizes="(max-width: 599px) 100vw, 599px" /> 

There are probably a million different ways to solve this problem. Â With ControlUp there is one obvious solution but we&#8217;ll work through each of them and what would be required. Â With ControlUp we want to target a user. Â The default &#8216;Assigned to&#8217; selectionÂ has 3 simple options and an &#8216;Advanced&#8217; option. Â The three simple options are &#8216;Computer&#8217;, &#8216;Session&#8217; and &#8216;Process&#8217;. Â<img class="aligncenter size-full wp-image-1665" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.51.14-PM.png" alt="Screen Shot 2016-08-30 at 9.51.14 PM" width="602" height="448" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.51.14-PM.png 602w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.51.14-PM-300x223.png 300w" sizes="(max-width: 602px) 100vw, 602px" /> 

Each of these elements corresponds to the different &#8216;views&#8217; that ControlUp has which allow the SBA to target items within that view.

<img class="aligncenter size-full wp-image-1667" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.53.59-PM.png" alt="Screen Shot 2016-08-30 at 9.53.59 PM" width="734" height="34" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.53.59-PM.png 734w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.53.59-PM-300x14.png 300w" sizes="(max-width: 734px) 100vw, 734px" /> 

But what about the other elements? Folders, Hosts, Accounts and Executables?

Enter the &#8216;Advanced&#8217; assigned to selection:

<img class="aligncenter size-full wp-image-1668" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.58.01-PM.png" alt="Screen Shot 2016-08-30 at 9.58.01 PM" width="497" height="102" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.58.01-PM.png 497w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-9.58.01-PM-300x62.png 300w" sizes="(max-width: 497px) 100vw, 497px" /> 

&nbsp;

This allows you to execute scripts against a larger range of views. Â The different &#8216;assigned to&#8217;Â have different execution contexts. Â The different contexts and assigned actions are:

<img class="aligncenter size-full wp-image-1669" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.06.17-PM.png" alt="Screen Shot 2016-08-30 at 10.06.17 PM" width="412" height="115" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.06.17-PM.png 412w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.06.17-PM-300x84.png 300w" sizes="(max-width: 412px) 100vw, 412px" /> 

Circling back to our problem, group assignment is found by querying Active Directory. Â This requires security permissions to read the user&#8217;s &#8220;memberOf&#8221; AD property. Â Odds are all fourÂ security contexts would have the rights to find this information but theyÂ will have to go through different ways to accomplish this. Â Going through the options, &#8220;<span style="text-decoration: underline;"><strong>User Session</strong></span>&#8221; would execute in the target&#8217;ed users security context so the rights the user has access to would be available. Â This includes access to the MemberOf property. Â Microsoft has created numerous methods for a user to query their own MemberOf attribute. Â The easiest maybe via RSAT tools (if they are installed on your server), PowerShell, and/or VBS. Â The RSAT tools give you utilities that can be executed from theÂ command prompt. Â Since I prefer PowerShell I&#8217;m going to continue this example using it. Â The command line for this is:

<pre class="lang:ps decode:true">([ADSISEARCHER]"samaccountname=$($env:USERNAME)").Findone().Properties.memberof -replace '^CN=([^,]+).+$','$1'</pre>

<img class="aligncenter size-full wp-image-1670" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.24.44-PM.png" alt="Screen Shot 2016-08-30 at 10.24.44 PM" width="600" height="448" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.24.44-PM.png 600w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.24.44-PM-300x224.png 300w" sizes="(max-width: 600px) 100vw, 600px" /> 

&nbsp;

<img class="aligncenter size-full wp-image-1671" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.25.52-PM.png" alt="Screen Shot 2016-08-30 at 10.25.52 PM" width="598" height="447" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.25.52-PM.png 598w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.25.52-PM-300x224.png 300w" sizes="(max-width: 598px) 100vw, 598px" /> 

Since I&#8217;m executing this in the context of the target&#8217;s &#8220;User Session&#8221; it will pick up all variables from that user, including the **$env:USERNAME**, which is the variable we need to populate to find the user within AD. Â Since we don&#8217;t need to specify a variable because we are pulling it from within the user&#8217;s context, we don&#8217;t need to set a &#8216;Argument&#8217; within the SBA.

<img class="aligncenter size-full wp-image-1672" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.28.00-PM.png" alt="Screen Shot 2016-08-30 at 10.28.00 PM" width="599" height="447" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.28.00-PM.png 599w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.28.00-PM-300x224.png 300w" sizes="(max-width: 599px) 100vw, 599px" /> 

&nbsp;

How does it run?

<div style="width: 1026px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1682-54" width="1026" height="538" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/08/What-Group.m4v?_=54" /><a href="http://theorypc.ca/wp-content/uploads/2016/08/What-Group.m4v">http://theorypc.ca/wp-content/uploads/2016/08/What-Group.m4v</a></video>
</div>

Pretty well. Â Without worrying about permissions this script executes and returns the output we are looking for. Â How does it do this? Â When you select the &#8216;User Session&#8217; Context, ControlUp will pass the script to the local system (WSCTXAPP303T in my video) and impersonate the user and execute the PowerShell command.

Let&#8217;s change this now. Â If we wanted to find the user&#8217;s group memberships but restrict it so that only ControlUp admins within an elevated group could see them we could change the &#8216;Execution Context&#8217; to &#8216;ControlUp Console (Current User)&#8217;.

<img class="aligncenter size-full wp-image-1676" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.40.01-PM.png" alt="Screen Shot 2016-08-30 at 10.40.01 PM" width="598" height="280" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.40.01-PM.png 598w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.40.01-PM-300x140.png 300w" sizes="(max-width: 598px) 100vw, 598px" /> 

This requires a couple modifications to the script as well. Â Now that we&#8217;ve chosen &#8216;ControlUp Console&#8217; this script will execute on the same box that you are running ControlUp from. Â If we leave the script as-is it will only return \*your\* group memberships. Â So we need to pass the script the ability to search for our target. Â Hello Arguments.

ControlUp allows you to take any field it presents as a argument to pass to your script. Â When in your script the argument is a variable &#8220;$args[#]&#8221; Â The &#8220;#&#8221; equals the argument you specify. Â In order to facilitate querying our user the script must be modified to accept it. Â Here is our new script:

<pre class="lang:ps decode:true">([ADSISEARCHER]"samaccountname=$(<span style="text-decoration: underline;"><strong>$args[0]</strong></span>)").Findone().Properties.memberof -replace '^CN=([^,]+).+$','$1'</pre>

I bolded and underlined the change. Â Within our SBA we set our &#8216;Arguments&#8217;:

<img class="aligncenter size-full wp-image-1677" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.49.15-PM.png" alt="Screen Shot 2016-08-30 at 10.49.15 PM" width="601" height="334" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.49.15-PM.png 601w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-10.49.15-PM-300x167.png 300w" sizes="(max-width: 601px) 100vw, 601px" /> 

And then we run it:

<div style="width: 1026px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1682-55" width="1026" height="530" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v?_=55" /><a href="http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v">http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v</a></video>
</div>

Well&#8230; Â That didn&#8217;t work. Â Why not?

ControlUp is the ultimate WYSIWYG application. Â The Argument we are passing, &#8220;User&#8221; is as shown in the video: &#8220;HEALTHY\trententtye&#8221;. Â The script is only expecting the samaccountname property but we have two properties; the User Domain (HEALTHY), a slash, and the SAM Account Name (trententtye). Â Since we are executing on the ControlUp console (because that is our execution context) we can see this in action by using Process Monitor and recording our action on that system:

<div style="width: 1026px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-1682-56" width="1026" height="530" preload="metadata" controls="controls"><source type="video/mp4" src="http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v?_=56" /><a href="http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v">http://theorypc.ca/wp-content/uploads/2016/08/What-Group2.m4v</a></video>
</div>

What&#8217;s in that PS1 file? Â Simply, the script you specified in ControlUp. Â Now that we have the script and the variables we can run this &#8216;offline&#8217; to debug it.

<img class="aligncenter size-large wp-image-1679" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.10.40-PM-1024x590.png" alt="Screen Shot 2016-08-30 at 11.10.40 PM" width="1024" height="590" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.10.40-PM-1024x590.png 1024w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.10.40-PM-300x173.png 300w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.10.40-PM-768x442.png 768w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.10.40-PM.png 1080w" sizes="(max-width: 1024px) 100vw, 1024px" /> 

And modifying the different variables gets us our results:

<img class="aligncenter size-full wp-image-1680" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.12.07-PM.png" alt="Screen Shot 2016-08-30 at 11.12.07 PM" width="667" height="64" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.12.07-PM.png 667w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.12.07-PM-300x29.png 300w" sizes="(max-width: 667px) 100vw, 667px" /> So, to make this work we need to remove the USERDOMAIN portion of our script. Â We can do that using &#8216;split&#8217; in Powershell:

<pre class="lang:ps decode:true ">$username = ($args[0] -split "\\")[1]
([ADSISEARCHER]"samaccountname=$($username)").Findone().Properties.memberof -replace '^CN=([^,]+).+$','$1'</pre>

This identifies the &#8220;\&#8221; between HEALTHY and trententtye as a delimiter and assigned each as a item within an array. Â By encompassing the expression in &#8220;()&#8221; we can directly reference the \*second\* item (first being &#8220;[0]&#8221;) and assign that to our username variable.

Now we test our script:

<img class="aligncenter size-full wp-image-1681" src="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.18.33-PM.png" alt="Screen Shot 2016-08-30 at 11.18.33 PM" width="910" height="625" srcset="http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.18.33-PM.png 910w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.18.33-PM-300x206.png 300w, http://theorypc.ca/wp-content/uploads/2016/08/Screen-Shot-2016-08-30-at-11.18.33-PM-768x527.png 768w" sizes="(max-width: 910px) 100vw, 910px" /> 

Perfect. Â So what are the Pro&#8217;s and Con&#8217;s to these two methods?

PROS:

The ControlUp Console context:  
Script is executed locally (The box ControlUp is running on may have a better connection to AD?)  
You can control permissions via Active Directory on whether the admins have permissions to view &#8216;MemberOf&#8217; property of the target user  
May fail if you don&#8217;t have permissions

The &#8216;User Session&#8217; context:  
It will always return the usersÂ groups  
Simpler script  
Run remotely where the user is located

CONS:

The ControlUp Console context:  
Script is executed locally (The remote/target serverÂ may have a better connection to AD?)  
CanÂ fail if you don&#8217;t have permissions  
Script is more complicated

The &#8216;User Session&#8217; context:  
It will always return the usersÂ groups, regardless of the Admin&#8217;s privileges.  
Run remotely where the user is located (the remote server may have a poor connection to AD)

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->