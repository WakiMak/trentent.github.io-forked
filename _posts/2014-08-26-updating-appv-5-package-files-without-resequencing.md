---
id: 596
title: Updating AppV 5 package files without resequencing
date: 2014-08-26T15:19:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2014/08/26/updating-appv-5-package-files-without-resequencing/
permalink: /2014/08/26/updating-appv-5-package-files-without-resequencing/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2014/08/updating-appv-5-package-files-without.html
blogger_internal:
  - /feeds/7977773/posts/default/929504855532575927
categories:
  - Blog
  - Uncategorized
tags:
  - AppV
---
With AppV 5.0 HF4 you can update the files inside a package without having to go through resequencing. Â The only catch is you have to prestage the file you want to replace/update first in the location you want, and I don&#8217;t think you can update files in the PVAD, only VFS.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://1.bp.blogspot.com/-LE3CJdwHKcU/U_z5cQsv-KI/AAAAAAAAAgI/t0EPKahnbPU/s1600/1.png"><img src="http://1.bp.blogspot.com/-LE3CJdwHKcU/U_z5cQsv-KI/AAAAAAAAAgI/t0EPKahnbPU/s1600/1.png" width="320" height="223" border="0" /></a>
</div>

To update a file in a .appv package, open the .appv in the sequencer and select the &#8220;Edit Package&#8221; option and click &#8220;Next&#8221;.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-xu4ySsg5r3A/U_z5eZ5QyDI/AAAAAAAAAgU/nrasPohX3fQ/s1600/2.png"><img src="http://4.bp.blogspot.com/-xu4ySsg5r3A/U_z5eZ5QyDI/AAAAAAAAAgU/nrasPohX3fQ/s1600/2.png" width="320" height="224" border="0" /></a>
</div>

Click &#8220;Edit&#8221;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-reY-x8aISs8/U_z5eS4ToNI/AAAAAAAAAgM/wPn4u7n59ms/s1600/3.png"><img src="http://4.bp.blogspot.com/-reY-x8aISs8/U_z5eS4ToNI/AAAAAAAAAgM/wPn4u7n59ms/s1600/3.png" width="320" height="130" border="0" /></a>
</div>

Expand the path to the file you want to update and right-click and select &#8220;Delete&#8221;.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-ibCYgUzVlCs/U_z5eVIXfWI/AAAAAAAAAgQ/nQ50JMF_-dw/s1600/4.png"><img src="http://2.bp.blogspot.com/-ibCYgUzVlCs/U_z5eVIXfWI/AAAAAAAAAgQ/nQ50JMF_-dw/s1600/4.png" width="320" height="262" border="0" /></a>
</div>

Right-click the folder above and select &#8220;Add&#8221;

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://2.bp.blogspot.com/-nFtAbS0xD8c/U_z5ewtmLXI/AAAAAAAAAgY/EIpWHPXPH4E/s1600/5.png"><img src="http://2.bp.blogspot.com/-nFtAbS0xD8c/U_z5ewtmLXI/AAAAAAAAAgY/EIpWHPXPH4E/s1600/5.png" width="320" height="198" border="0" /></a>
</div>

Browse to the file, select it, click &#8220;Open&#8221;.

<div style="clear: both; text-align: center;">
  <a style="margin-left: 1em; margin-right: 1em;" href="http://4.bp.blogspot.com/-rvkbEp6GFFk/U_z5fb1_HSI/AAAAAAAAAgc/tmB-w1Q3P6M/s1600/6.png"><img src="http://4.bp.blogspot.com/-rvkbEp6GFFk/U_z5fb1_HSI/AAAAAAAAAgc/tmB-w1Q3P6M/s1600/6.png" width="320" height="183" border="0" /></a>
</div>

Click &#8220;OK&#8221; to make a new VFS mapping. Â Choose File > Save and save your package.

If you have the original package and rename it to .zip from .appv and get properties on your file, and do the same on your new version of the package (after making a copy of it) you should see that they are different and you have a new file in the new version without having to &#8220;Update&#8221; the package; avoiding capturing additional garbage that happens when you update an application.

<div>
</div>

<div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->