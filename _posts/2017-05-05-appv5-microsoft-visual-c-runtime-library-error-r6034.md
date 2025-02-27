---
id: 2211
title: AppV5 - Microsoft Visual C++ Runtime Library - Error R6034
date: 2017-05-05T13:38:15-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/?p=2211
permalink: /2017/05/05/appv5-microsoft-visual-c-runtime-library-error-r6034/
image: /wp-content/uploads/2017/05/Icon.png
categories:
  - Blog
tags:
  - 2008R2
  - AppV
  - procmon
---
We packaged an application that was giving out an error message at a certain point in the program:

![](/wp-content/uploads/2017/05/RuntimeError.png)
```plaintext
--------- Microsoft Visual C++ Runtime Library --------- 
	Runtime Error! 
	Program: D:\AppVData\PackageInstallationRoot\7336984D-A43A-4EE3-A223-09E50C2E4A04\8B9E7007-D87F-4620-9B10-F06320BB1B... R6034 
	An application has made an attempt to load the C runtime library incorrectly. Please contact the application's support team for more information. 
--------- OK ---------
```

&nbsp;

Fortunately, this was a nice repeatable error.

What happens is the program generates this message when it's importing a word doc into its database.  This error message doesn't stop the program from importing or causes a failure, and the process actually completes successfully.  Even stranger is after this message appears and you click OK it will not pop up again and you can repeat the process ad infinitum without this prompt...  Until you quit and relaunch the program.  Then it will prompt the first time but then never after.

But having this prompt even once is unacceptable to our users.  So how can we solve it?  Let's examine the clues we've been given.  "An application has made an attempt to load the C runtime library incorrectly".  So it's crashing when loading a Visual C++ Runtime Library.  Which file?  Unfortunately, the path got truncated.  But Procmon will tell us!  I opened procmon, set it only look for 'Load Image' operations and then traced the process name.  This is where it crashed:

<img class="aligncenter size-full wp-image-2213" src="/wp-content/uploads/2017/05/Last_Operation.png" alt="" width="1288" height="146" srcset="/wp-content/uploads/2017/05/Last_Operation.png 1288w, /wp-content/uploads/2017/05/Last_Operation-300x34.png 300w, /wp-content/uploads/2017/05/Last_Operation-768x87.png 768w" sizes="(max-width: 1288px) 100vw, 1288px" /> 

&nbsp;

It crashed loading the MSVCR80.dll.  What's curious is this DLL and the version and everything exist natively in the system.  I'm unsure why it was captured but the program is deferring to the AppV msvcr80.dll as opposed to the natively installed one.

Using Process Explorer I searched for this DLL and confirmed that it's loaded in multiple places, with all programs except the AppV program using the natively installed one.

<img class="aligncenter size-full wp-image-2214" src="/wp-content/uploads/2017/05/DLLSearch.png" alt="" width="1284" height="728" srcset="/wp-content/uploads/2017/05/DLLSearch.png 1284w, /wp-content/uploads/2017/05/DLLSearch-300x170.png 300w, /wp-content/uploads/2017/05/DLLSearch-768x435.png 768w" sizes="(max-width: 1284px) 100vw, 1284px" /> 

What I suspect is happening is that there is some form of DLL sharing occurring and Windows is telling the program that "your loading this file?!  I already have it loaded and used by all these other programs.  Use that instead you dummy!"

So can we force the program to use the natively installed DLL's?

This program automatically installs some Visual C++ runtime libraries and these are captured by AppV automatically.  There is no way to remove them as they are not a separate installer or anything we can just hack out during an install.

However, we can just manually delete them.  If they don't exist in the package AppV should automatically look for those files in the native file system.

I then went into the AppV Sequencer and 'Edit' on the package, selected 'Package Files' and deleted the 'winsxs' folder:

<img class="aligncenter size-full wp-image-2215" src="/wp-content/uploads/2017/05/Delete_folder.png" alt="" width="1304" height="486" srcset="/wp-content/uploads/2017/05/Delete_folder.png 1304w, /wp-content/uploads/2017/05/Delete_folder-300x112.png 300w, /wp-content/uploads/2017/05/Delete_folder-768x286.png 768w" sizes="(max-width: 1304px) 100vw, 1304px" /> 

&nbsp;

Did it work?

This is what procmon showed me for the new package without these folders:

<img class="aligncenter size-full wp-image-2216" src="/wp-content/uploads/2017/05/success.png" alt="" width="1261" height="145" srcset="/wp-content/uploads/2017/05/success.png 1261w, /wp-content/uploads/2017/05/success-300x34.png 300w, /wp-content/uploads/2017/05/success-768x88.png 768w" sizes="(max-width: 1261px) 100vw, 1261px" /> 

The msvcm80.dll is now using the natively installed version and path!  And I did not get the error message any more and the operation worked successfully!  Another success story.

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->