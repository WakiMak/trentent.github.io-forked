---
id: 685
title: Issue with Folder Redirection (Error 267)
date: 2012-02-29T16:46:00-06:00
author: trententtye
layout: post
guid: 'http://theorypc.ca/blog/2012/02/29/issue-with-folder-redirection-error-%267/'
permalink: /2012/02/29/issue-with-folder-redirection-error-7/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2012/02/issue-with-folder-redirection-error-267.html
blogger_internal:
  - /feeds/7977773/posts/default/8965480642273981945
categories:
  - Blog
  - Uncategorized
tags:
  - "2003"
---
So we were having an issue with Folder Redirection today with a user. We were moving them from a fileshare to a UNC path. The difference is a file share is explicitly shared, the userdirs$ is a share that then has a folder within.

This was the error in fdeploy.log (enabled here).

> <pre class="lang:default decode:true ">16:32:10:116 Homedir redirection path %HOMESHARE%%HOMEPATH% expanded to \newserveruserdirs$user1.
16:32:10:116 Redirecting folder My Documents to \%HOMESHARE%%HOMEPATH%.
16:32:10:116 Previous folder path \oldserveruser1$ expanded to \oldserveruser1$.
16:32:10:116 New folder path \%HOMESHARE%%HOMEPATH% expanded to \newserveruserdirs$user1.
16:32:11:007 Failed to perform redirection of folder My Documents.
The folder is configured to be redirected from &lt;\oldserveruser1$&gt; to &lt;\newserveruserdirs$user1&gt;.
The following error occurred:
%%267</pre>

%%267. After some brief investigation it was found this users had a huge My Docs and it was copied to the new server. The old directory was then renamed (user1.old) and a new directory was created called user1 and reshared as user1$ on the old server. Then this issue occurred. The reason this issue occurred was because the user wasn&#8217;t assigned any permissions on the old directory so it was erroring out. I don&#8217;t know if %%267 is a permission code or not but that&#8217;s what we found. By adding the user to the folder \oldserveruser1$ as full control did it then proceed and migrate his My Docs correctly.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->