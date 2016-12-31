---
layout: post
title: Virtual Reality
---

## {{ page.title }}


- - - -

#Summary

These are my notes from the Virtual Reality Nanodegree course on Udacity. What creates immersion in VR?

##Pieces

Let's look at the hardware:

* Lenses
* Displays
* Tracking

###Lenses

VR lenses are specially curved and placed (near-eyed display helps magnify the images)

* Lenses trade off between field of view, focal length, comfort, optical distortion, and cost
* Without lenses, your eyes won't focus to the display (e.g. looking at phone really closely does nothing)

###Displays

Behind the lenses are the OLED displays

* Displays support **low-persistance**, a moving slice/portion of the world instead of a full image
* Low-persistance helps trick your brain so it doesn't get motion blur when you look around, minimizing sickness

###Positional Tracking

Positional Tracking is how the computer knows where you are.

* IMUs tell how you are rotated only (not where you are)
* VR requires some type of positional tracking

##Mobile vs Desktop VR

**Degrees of Freedom** is the difference between Mobile (3-DOF) and Desktop (6-DOF) tracking.

* 3-DOF means your head rotations are accurately tracked; i.e. where you are looking
* 6-DOF means also where you are around the room; i.e. where you are looking and where you are

###IMUs with 3-DOF

Inertial Measurement Unit (IMU) is a tiny sensor that is really good at detecting rotations, combining together:

* accelerometer
* magnetometer
* gyroscope

Combines data and uses gravity + earth's magnetic field to detect direction its facing. Tells you 3-DOF

###Tracking with 6-DOF

There's a lot of different ways to track with 6-DOF.

####Oculus Rift's Constellation

* Oculus Rift uses a tracking system called **Constellation**
* OR has a lot of LEDs that blink really fast in a specific pattern
* A camera captures the light positions and builds a model
* Model fits what it sees and tries to calculate where you are

####Vive's Lighthouse

* Vive also uses infared light, but instead uses infared lasers
* There's two base stations called 'lighthouses' that make vertical and horizontal sweeps across the room
* Combines this data to track headseat and controllers

##VR Development Platforms

You can create code directly or use a game engine platform.

* A game engine is a software development framework that makes it easier to develop games
* Game engines are packages of tools that help make development time shorter
* Game engines include Unity, Unreal
* You can also build games directly using native development
* Native Development could be in OpenGL, Microsoft DirectX
* Results are much more flexibility, but much longer development times

#Creating Scenes and Objects

We learn how to create a scene by creating an apartment by creating objects, making objects animate, and view scenes with cameras and lights.

##Meshes

Everything in VR is made of points and those points are connected together using triangles.
Triangles joined together form **meshes**, which is the underlying foundation for your scenes.
Triangles are used for 3d objects based on their speed, simplicity, and also on convention.

##Primitives

Cubes, cones, planes are called **primitives**.
Simple models can be created under 'GameObjects' > '3D Object' > e.g. Cube, Sphere, Capsule, Cylinder, Plane, Quad
If you want a complex model (e.g. a rat, a person, a tree), then you need a program like **Blender** or **Maya**

##Transforms

You can **transform** primitives by **position**, **scale**, and **rotation**.
Transforms can be nested, think like a robot arm with multiple joints (Unity calculates using **matricies**)
These transforms use a **left handed co-ordinate system**, meaning with your left hand, point up with your index finger and 90 degrees point right with your thumb.

* Y means up and down
* X means right and left
* Z means forward or backwards (depth)

Transforms are accessible in Unity in a 'Transform' window.


