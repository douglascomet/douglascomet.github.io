---
layout: post
title: Water Spline in UE4
tags: [blueprints, ue4]
---

This blueprint produces a plane or cylinder to make the water look like a river, waterfall, or river based on level design needs. Each control point can be scaled and be positioned.

Material by [Yunhao Huo](https://yunhao-huo.com/)

<p>
<iframe align="center" width="560" height="315" src="http://www.youtube.com/watch?v=-I0J-XnSAeE" frameborder="0" allowfullscreen></iframe>
</p>

This post is meant to explain my approach to procedurally generating rivers, lakes, and waterfalls along a spline in UE4.

# Blueprint Breakdown

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineConstructionScript.PNG">

## The Problem

For the [Hollowed](http://store.steampowered.com/app/669630/Hollowed/) project, there was a need to have water being able to defy gravity. Since the game was designed to be in a fantastical world, level designers needed freedom to define the look. The development for this project was only a few months and we were constantly iterating therefore the only solution was to procedurally make rivers and waterfalls.

I initially wanted to utilize Houdini Digital Assets for this but I encountered issues getting HDA's into the engine. I moved on to looking to see if UE4 could produce the flexibility I was looking for.

I came across tutorials these tutorials:

Zak Parrish's Spline Demo was especially informative and served as the basis for my spline blueprint.

[https://docs.unrealengine.com/latest/INT/Videos/PLZlv_N0_O1ga0aV9jVqJgog0VWz1cLL5f/wR0fH6O9jD8/](https://docs.unrealengine.com/latest/INT/Videos/PLZlv_N0_O1ga0aV9jVqJgog0VWz1cLL5f/wR0fH6O9jD8/)

[https://www.youtube.com/watch?v=kJyWSzfwEk8](https://www.youtube.com/watch?v=kJyWSzfwEk8)

[https://www.youtube.com/watch?v=7YUxM0NDWRY](https://www.youtube.com/watch?v=7YUxM0NDWRY)

## My Solution

<img align="right" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataStruct.png">

As described in Zak Parrish's tutorial, in order to be able to scale and rotate spline points the data must be defined and fed into the points. I created a data structure to define the behavior at each point, however typically splines generate points without access to any other data besides translation. For this to work I had to create a custom functions that creates spline with my additional data.

This was all done in the construction script so the level designers can continually modify it.

Splines are essentially an array of points meaning I would need an array of my data structure to be able to feed the data into the points. As shown the in construction script above I sequence out into a few stages of building the water spline.

<img align="right" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineExposedParameters.PNG"  width="35%" height="35%">

The first function is a simple one storing the number of the spline points to a variable.

<img align="left" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineStoreSplinePoints.PNG" width="60%" height="60%">

Splines are essentially an array of points meaning I would need an array of my data structure to be able to feed the data into the points. So in my custom "Build Water Spline Element" function I match the size of an array variable of the data structure to number of points available. To account for the user cases of making a river and a waterfall, I exposed a boolean in order to give the user the option to toggle choose between a plan or a cylinder that is instanced along the spline.

The Blue Section of this function is the first element of the sequence where every point of the spline is recorded and the parameters of the data structure for each point is set. The data structure array variable is exposed to the user and can be seen in the image to the right. The user searches for the point and can modify the values directly. This is a slightly annoying solution because scale and rotate don't work intuitively with a regular spline but the results are exactly what I want.

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataSequence11.PNG">

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataSequence12.PNG">

The Grey Section and the second element of the sequence in the custom "Build Water Spline Element" function is the logic needed to attach the designated mesh to the spline and connect the values in the blueprint to the exposed values.

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataAttachToSpline11.PNG">

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataAttachToSpline12.PNG">

<img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineBuildWaterDataAttachToSpline13.PNG">

 Just like there being an inherent array of points for the spline and an array of meshes attached to the spline, there needs to be an array of dynamic materials to match the number of meshes. The third element of the sequence leads to a series of set material parameter values. The exposed variables for the material are fed into each element of the dynamic material array. The only issue I came across wanting to scale the UVs. To ensure the material's textures would tile as expected, the UVs need to be scaled in relation to where mesh and its UVs are along the splines 0-1 range. There was not enough time in development to figure this out but the results were good enough that is was not necessary.

 <img align="center" src="https://douglascomet.github.io/blog/images/WaterSpline/WaterSplineDynamicMaterial.PNG">
