---
layout: post
title: Scene Depth Post Process Material in UE4
tags: [Materials, UE4]
---

This post is meant to explain my approach to creating layers of colored fog using a post process material in UE4.

# Material Breakdown

<img align="center" src="../blog/images/CustomPPM/Custom Scene Depth Post Process.PNG">

The visual aesthetic of the capstone game, [Hollowed](http://store.steampowered.com/app/669630/Hollowed/), drew inspiration from the work of [Mikael Gustafsson](https://www.assetstore.unity3d.com/en/#!/search/page=1/sortby=popularity/query=publisher:13598).

<div>
<img align="left" src="../blog/images/CustomPPM/deer-anim3.gif" width="350" height="350">

<img align="right" src="../blog/images/CustomPPM/snp-2.gif" width="350" height="350">
</div>

Together with [Leah Augustine](https://leahaugustine.com/), the Art Lead for this project, we dissected the aspects of the aspects that contributed to his graphical look. Fortunately for us Gustafsson also sold an asset pack he created for Unity that demonstrated his workflow. He used 3D assets within Unity to create vibrant imagery with subtle movements. That asset pack can be found here: [Mikael Gustafsson's Unity Store Page](https://www.assetstore.unity3d.com/en/#!/search/page=1/sortby=popularity/query=publisher:13598)

We determined we need those layers "colored fog" so I began to research ways to implement this. Forum posts like these served as the basis for my implementation:

[https://answers.unrealengine.com/questions/210553/multicolored-fog-post-process-material.html](https://answers.unrealengine.com/questions/210553/multicolored-fog-post-process-material.html)

[https://storyteller-vr.com/2015/11/22/exponential-squared-fog-for-unreal-engine-4/](https://storyteller-vr.com/2015/11/22/exponential-squared-fog-for-unreal-engine-4/)

[https://sainarayan.me/distance-fog-in-ue4/](https://storyteller-vr.com/2015/11/22/exponential-squared-fog-for-unreal-engine-4/)

## My Solution

The red, blue, green, and black sections of my node network are almost identical but vary with parameter inputs. Starting with the red section, a color parameter is set to LERP between the masked RGB output of the Scene Texture. This allows the background to be unaffected by the post process material.
​
The alpha for color/Scene Texture LERP is determined by the quotient of the scene depth and an arbitrary parameter that the user determines as a point in worldspace. The quotient is then saturated to minimize the range of the resulting value.

<img align="left" src="../blog/images/CustomPPM/Custom Scene Depth Post Process Section Example.PNG">

At the bottom of the red section, the camera distance from the scene's objects is determined by taking the absolute world position and finding the distance from the camera position. This was then used to calculate the quotient by dividing by the LERP version of an arbitrary parameter that the user determines as a point in the worldspace. In order to make this result more noticeable, the result is put into a power node and finally saturated to limit the range of the resulting value.

 The output of the color LERP and the arbitrary parameter that the user determines as a point in worldspace are LERP-ed in in the subsequent sections.
​
The output of the camera distance is fed into each section in an identical manner.section in an identical manner.

<img align="left" src="../blog/images/CustomPPM/Custom Scene Depth Post Process Result Example.PNG">

The Final Blend section takes the results of all the sections and LERPs the results against the Scene Texture in the foreground and the user defined Skysphere, far, medium, and near distances.
​
By making a material instance, the user can determine where these sections begin, blend together, and which colors those sections are.

The Player Stencil section was added specifically for this project to allow Halia, the protagonist of [Hollowed](http://store.steampowered.com/app/669630/Hollowed/), to be unaffected by this post process material.
