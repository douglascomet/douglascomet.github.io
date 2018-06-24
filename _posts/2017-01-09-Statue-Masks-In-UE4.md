---
layout: post
title: Statue Masks in UE4
tags: [materials, blueprints, ue4]
---

Here is a demonstration of dynamic materials controlled through blueprint input. For gameplay purposes, the textures on the statues are animated to visually trigger events in the game, [Hollowed](http://store.steampowered.com/app/669630/Hollowed/).

<p>
<iframe width="560" height="315" src="https://www.youtube.com/embed/I4crEoGJUcI" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</p>

This post is meant to explain my approach to show how I animated statue materials in UE4.

# Sphere Mask Node Network Breakdown

<img align="center" src="https://douglascomet.github.io/blog/images/StatueMasks/StatueSphereMaskBreakdown.PNG">

# Vertical Mask Node Network Breakdown

<img align="center" src="https://douglascomet.github.io/blog/images/StatueMasks/StatueVerticalBreakdown.PNG">

For the [Hollowed](http://store.steampowered.com/app/669630/Hollowed/) project,we had statue mesh's that were meant to signify different aspects of the Stages of Grieving. The poses and color of the statue's emissive were designed to emphasize these Stages.

Purple - Denial, Red - Anger, Blue - Bargaining, Grey - Depression​

In terms of gameplay the character, Halia, must offer her spirit, Oco, to these statues to trigger events in the game.​

Originally the statues would be illuminated gradually and uniformly. I wanted to setup a way for the statues to activate and deactivate. Based on where Oco is set to interact with the statues, Denial, Anger and Depression used the spherical mask that emanates from a point in world space. Oco interacts with the Bargaining statue in the empty hand and rather than using a sphereical mask I chose to use a vertical reveal. I wanted the bag in the statues other hand to be revealed last and a sphereical mask wouldn't have accomplished that.

<div id='gifs'>
<img align="left" src="https://douglascomet.github.io/blog/images/StatueMasks/DenialOn2.gif" width="350" height="350">

<img align="right" src="https://douglascomet.github.io/blog/images/StatueMasks/DenialOff2.gif" width="350" height="350">

<img align="left" src="https://douglascomet.github.io/blog/images/StatueMasks/AngerOn2.gif" width="350" height="350">

<img align="right" src="https://douglascomet.github.io/blog/images/StatueMasks/AngerOff2.gif" width="350" height="350">

<img align="left" src="https://douglascomet.github.io/blog/images/StatueMasks/BargainingOn2.gif" width="350" height="350">

<img align="right" src="https://douglascomet.github.io/blog/images/StatueMasks/BargainingOff2.gif" width="350" height="350">

<img align="left" src="https://douglascomet.github.io/blog/images/StatueMasks/DepressionOn2.gif" width="350" height="350">

<img align="right" src="https://douglascomet.github.io/blog/images/StatueMasks/DepressionOff2.gif" width="350" height="350">
</div>

## My Process

I highlighted aspects of the blueprint these statues are used in to show the emissive masks is controlled. The only difference in the blueprint implementation is that the Bargaining didn't need a vector input. Within the blueprint for each of these statues in the construction script I had to make the mesh component's material dynamic. I originally tried to set up this step in the Event graph under the "Begin Play" Event but there material tend to be broken when the game began.

In the example of the construction script shown below, there were two mesh components so they were both set to dynamic, but each of the other statues match this flow of logic.

<img align="center" src="https://douglascomet.github.io/blog/images/StatueMasks/StatueSphereMaskConstructionScript.PNg">

Louis Hofer, the Programming Lead for Hollowed, set up the rest of the blueprint not shown here but also made a very easy way to animate and feed information into the materials of the statues. By using a single timeline with linear slope from 0-1 the activation and deactivation of the statues could be animated. The level designer would use the exposed parameter "Start Emissive" to define the initial values of the statues. Zero would mean the statue would be off and the timeline would animate to One and illuminate the statue. Conversely if the statue was initially set to One and be illuminated, the timeline would animate to zero and turn off.

<div>
<img align="center" src="https://douglascomet.github.io/blog/images/StatueMasks/StatueSphereMaskEvents.PNG">

<img align="right" src="https://douglascomet.github.io/blog/images/StatueMasks/StatueSphereMaskEventCalls.PNG">
</div>

In this blueprint, Louis used the "End Collision Component" to make calculations to determine when the character's spirit would overlap. I used that collision's volume's world location to feed into the vector parameter for the dynamic version of the mesh component's material so the sphere mask knew what point in space to work with.

The "Start Emissive" exposed variable not only determine how the statues were initialized but the value also determine which event using the timeline was called elsewhere in the blueprint.
