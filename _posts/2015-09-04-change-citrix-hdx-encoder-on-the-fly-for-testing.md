---
id: 550
title: Change Citrix HDX Encoder on the fly for testing
date: 2015-09-04T12:18:00-06:00
author: trententtye
layout: post
guid: http://theorypc.ca/blog/2015/09/04/change-citrix-hdx-encoder-on-the-fly-for-testing/
permalink: /2015/09/04/change-citrix-hdx-encoder-on-the-fly-for-testing/
blogger_blog:
  - trentent.blogspot.com
blogger_author:
  - Trentent
blogger_permalink:
  - /2015/09/change-citrix-hdx-encoder-on-fly-for.html
blogger_internal:
  - /feeds/7977773/posts/default/1200277394035916430
categories:
  - Blog
  - Uncategorized
tags:
  - Citrix
  - HDX

---
[Rachel Berry](https://virtuallyvisual.wordpress.com/) posted an article on [optimizing HDX for gaming.](https://virtuallyvisual.wordpress.com/2015/08/02/did-i-really-see-a-165-thin-client-doing-55fps-with-borderlands-2-with-hdx-3d-pro-citrix-xendesktop/) In this article she highlighted that Citrix has some 'special' registry keys for modifying [different parameters of the Thinwire encoder](https://virtuallyvisual.wordpress.com/2015/07/26/hdx-and-h-264-artefacts-in-citrix-xendesktop&/).  One of these keys was changing the encoder itself:

  * Encoder = 2 is Pure H.264 (YUV 4:2:0). As with most vendors this is H.264 4:2:0 format, it€™s designed for a balance of quality and bandwidth primarily on video and high-bandwidth CAD parts (not much text). This is used by the HDX 3D Pro VDA.
  * Encoder = 1 is H.264+lossless text. This is used by default by the XenDesktop standard VDA and & VDA.
  * Encoder = 0 forces you to use Compatibility mode

In terms used by HDX, it shakes out like so:

Encoder 2 = DeepCompressionEncoder  
Encoder 1 = DeepCompressionV2Encoder  
Encoder 0 = CompatibilityEncoder

The cool thing about these encoders is you can modify their values \*on the fly\* and it will take place immediately in your Citrix session. This video I made demonstrates this. I ran a 3D benchmark application and modified the encoder's on the fly. I zoomed into the FPS counter and put this in the bottom left corner as the text with moving images shows much better the difference in the encoders.  Without a doubt, the CompatibilityEncoder has the worst quality of all the encoders when it comes to video/moving images/3D/gaming.

Try to watch it in 1080p for maximum quality.

<div>
</div>

<div style="clear: both; text-align: center;">
</div>

<div>
  <div>
  </div>
  
  <div>
    To change the encoder, you need to add/modify a registry key at HKLM\Software\Citrix\Graphics.  The type is DWORD32 named "Encoder" and the value is 0x0 to 0x2, depending on the encoder you want to try and use.
  </div>
  
  <div>
    All testing was done with & 7.6 on my home built lab.
  </div>
</div>

<!-- AddThis Advanced Settings generic via filter on the_content -->

<!-- AddThis Share Buttons generic via filter on the_content -->