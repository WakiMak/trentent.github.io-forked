---
id: 2048
title: Citrix Workspace Environment Manager - Second Impressions
date: 2017-03-08T09:06:25-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2048
permalink: /2017/03/08/citrix-workspace-environment-manager-second-impressions/
image: /wp-content/uploads/2017/03/wem_icon2.png
categories:
  - Blog
tags:
  - Citrix
  - Performance
  - Registry
  - Workspace Environment Manager
---
[Workspace Environment Manager does not do Boolean logic](https://docs.citrix.com/content/dam/docs/en-us/workspace-environment-management/4-1/downloads/citrix-workspace-environment-management-4-1-administration-guide.pdf).  It only does AND statements.

<div class="page" title="Page 28">
  <div class="layoutArea">
    <div class="column">
      <blockquote>
        <p>
          Note: These conditions are <strong>AND</strong> statements, <span style="text-decoration: underline;"><strong>not</strong></span><strong> </strong><strong>OR</strong> statements. Adding multiple conditions will require all of them to trigger for the filter to be considered triggered.
        </p>
      </blockquote>
    </div>
  </div>
  
  <p>
    So we have to reconsider how we will apply some settings.  An example:
  </p>
  
  <p>
    <img class="aligncenter size-full wp-image-2050" src="http://theorypc.ca/wp-content/uploads/2017/03/wem10.png" alt="" width="1344" height="561" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem10.png 1344w, http://theorypc.ca/wp-content/uploads/2017/03/wem10-300x125.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem10-768x321.png 768w" sizes="(max-width: 1344px) 100vw, 1344px" />
  </p>
  
  <p>
    How would you apply this setting without WEM?  The setting is targeted to a specific set of servers that we've put into a group, AND the user must be a member of at least 1 out of about 10 groups for it to apply.  And this is what we've set.  It seems fairly logical and the the way the targeting editor is setup it's actually fairly easy to follow.
  </p>
</div>

How do we configure this in WEM?

WEM works by applying settings based on your individual user account or a group containing users.  So each group needs to be added to WEM and then configured individually.  Alternatively, this can change your thinking and you can create groups that you want settings to apply towards.  For instance, in this example I only have two statements I need to be true.

The server must belong to a particular group.  
The user must belong to at least one of several groups.

So I started by attempting to get to the result we wanted.

I exported the value as we wanted it from the registry and imported it into WEM:

<img class="aligncenter size-full wp-image-2051" src="http://theorypc.ca/wp-content/uploads/2017/03/wem11.png" alt="" width="994" height="547" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem11.png 994w, http://theorypc.ca/wp-content/uploads/2017/03/wem11-300x165.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem11-768x423.png 768w" sizes="(max-width: 994px) 100vw, 994px" /> 

Now to create our conditions. I want this registry action to only apply if you are on a specific server.  We add our servers into logical groups. I attempted to set the filter condition to target the server with this specific AD group. It turns out this was not possible with the builtin filter conditions.

<img class="aligncenter size-full wp-image-2052" src="http://theorypc.ca/wp-content/uploads/2017/03/wem12.png" alt="" width="478" height="550" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem12.png 478w, http://theorypc.ca/wp-content/uploads/2017/03/wem12-261x300.png 261w" sizes="(max-width: 478px) 100vw, 478px" /> 

The Active Directory filter conditions are only evaluated against the user and not the machine. However, there is an option for 'ComputerName Match':

<img class="aligncenter size-full wp-image-2053" src="http://theorypc.ca/wp-content/uploads/2017/03/wem13.png" alt="" width="312" height="159" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem13.png 312w, http://theorypc.ca/wp-content/uploads/2017/03/wem13-300x153.png 300w" sizes="(max-width: 312px) 100vw, 312px" /> 

Our server naming convention is CTX (for Citrix) APP or silo name (eg, EPIC) ### (3 digit numerical designation) and the letter "T" at the end for a test server or no letter for a production server. So our naming scheme would look like so:

CTXAPP301 - for a prod server  
CTXAPP301T - for a test server

The guide for WEM does specify you can use a wild card "*" to allow arbitrary matches. So I tested it and found it has limitations.

The wild card appears to only be valid if it's at the beginning or end of the name. For instance, setting the match string to:

CTXAPP3*

Will match both the test and prod servers. However, setting CTXAPP3*T and the wildcard is 'dropped' from the search (according to the WEM logs). So the search result is actually CTXAPP3T. Which is utterly useless. So a proper naming scheme is very important. Unfortunately, this will not work for us unless we manually specify all of our computer names. For example, we would have to do the following for this to work:

CTXAPP301T, CTXAPP302T, CTXAPP303T, etc.

<img class="aligncenter size-full wp-image-2054" src="http://theorypc.ca/wp-content/uploads/2017/03/wem14.png" alt="" width="481" height="542" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem14.png 481w, http://theorypc.ca/wp-content/uploads/2017/03/wem14-266x300.png 266w" sizes="(max-width: 481px) 100vw, 481px" /> 

Although this works, this is not workable. Adding/removing servers would require us to keep this filter updated fairly constantly. Is there another option?

<img class="aligncenter size-full wp-image-2055" src="http://theorypc.ca/wp-content/uploads/2017/03/wem15.png" alt="" width="304" height="159" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem15.png 304w, http://theorypc.ca/wp-content/uploads/2017/03/wem15-300x157.png 300w" sizes="(max-width: 304px) 100vw, 304px" /> 

Apparently, you can execute a WMI Query.  We can use this to see if the computer is a member of a group. The command to do so is:

<pre class="lang:default decode:true ">SELECT * FROM Win32_GroupUser Where GroupComponent="Win32_Group.Domain='HEALTHY',Name='AHS.CTX.Servers.Generic.TST.AHI'" AND PartComponent="Win32_UserAccount.Domain='HEALTHY',Name='CTXAPP301T$'"</pre>

WEM supports variable replacement through a list of [HASHTAGS](https://docs.citrix.com/en-us/workspace-environment-management/4-2/dynamic-tokens.html)

I modified my command as such:

<pre class="lang:default decode:true ">SELECT * FROM Win32_GroupUser Where GroupComponent="Win32_Group.Domain='HEALTHY',Name='AHS.CTX.Servers.Generic.TST.AHI'" AND PartComponent="Win32_UserAccount.Domain='HEALTHY',Name='##ComputerName##$'"</pre>

Executing this command does result in checking to see if the computer is a member of a specific group. The only caveat is the user account that logs on must have permissions within AD to check group memberships. Which is usually granted. And in my case, it is so this command works.

Next on the Group Policy Preference is the 'AND - OR'. We have the filter condition now that ensures these values will only be applied if the computer is in a certain group. Now we need the value to apply if the user is a member of a certain group.

An easy solution might be to create a super-group "HungAppTimeout" or some such and add all the groups I want that value to apply to that group.  Another alternative is we can 'configure' each user group with the 'server must belong to group' filter and that should satisfy the same requirements.  I chose this route for our evaluation of WEM to avoid creating large swaths of groups simply for the purpose of applying a single registry entry.

Instead of doing the 'OR', we select the individual groups that this check would be against and actually specify that we apply this settings to that group.

To do that we add each group to the 'Configured Users':

<img class="aligncenter size-full wp-image-2056" src="http://theorypc.ca/wp-content/uploads/2017/03/wem16.png" alt="" width="1390" height="652" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem16.png 1390w, http://theorypc.ca/wp-content/uploads/2017/03/wem16-300x141.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem16-768x360.png 768w" sizes="(max-width: 1390px) 100vw, 1390px" /> 

And then for each group, under 'Assignment' we apply our setting with the filter:

<img class="aligncenter size-full wp-image-2057" src="http://theorypc.ca/wp-content/uploads/2017/03/wem17.png" alt="" width="1390" height="710" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem17.png 1390w, http://theorypc.ca/wp-content/uploads/2017/03/wem17-300x153.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem17-768x392.png 768w" sizes="(max-width: 1390px) 100vw, 1390px" /> 

And now each group has this value applied with the appropriate conditions.

Continuing, we have the following policy:

<img class="aligncenter size-full wp-image-2058" src="http://theorypc.ca/wp-content/uploads/2017/03/wem18.png" alt="" width="1228" height="579" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem18.png 1228w, http://theorypc.ca/wp-content/uploads/2017/03/wem18-300x141.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem18-768x362.png 768w" sizes="(max-width: 1228px) 100vw, 1228px" /> 

So this filtering is applied to a collection, not the individual settings. The filtering checks to see if the computer is a member of a specific group of servers, and whether the user is a member of a specific group.

In order to accomplish this same result I have no choice but to create a parent group for the machines. Instead of an 'OR' we create a new group and add both server groups within it. This should result in the same effective 'OR' statement for the machine check, at least. Then we apply all the settings to the specific groups so only they get the values applied.

In total we have 154 total individual registry entries we apply.

<img class="aligncenter size-full wp-image-2059" src="http://theorypc.ca/wp-content/uploads/2017/03/wem19.png" alt="" width="1144" height="653" srcset="http://theorypc.ca/wp-content/uploads/2017/03/wem19.png 1144w, http://theorypc.ca/wp-content/uploads/2017/03/wem19-300x171.png 300w, http://theorypc.ca/wp-content/uploads/2017/03/wem19-768x438.png 768w" sizes="(max-width: 1144px) 100vw, 1144px" /> 

So how does it compare to Group Policy Preferences?

Stay Tuned 

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->
