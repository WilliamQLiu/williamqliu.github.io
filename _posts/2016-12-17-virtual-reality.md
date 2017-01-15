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

#Creating Scenes

We learn that a scene of an apartment is made up of objects, animations, cameras and lights.

##Objects

###Meshes

Everything in VR is made of points and those points are connected together using triangles.
Triangles joined together form **meshes**, which is the underlying foundation for your scenes.
Triangles are used for 3d objects based on their speed, simplicity, and also on convention.

###Primitives

Cubes, cones, planes are called **primitives**.
Simple models can be created under 'GameObjects' > '3D Object' > e.g. Cube, Sphere, Capsule, Cylinder, Plane, Quad
If you want a complex model (e.g. a rat, a person, a tree), then you need a program like **Blender** or **Maya**

###Materials

Materials describe the surface appearance of a 3D object. Without Materials, we can't even see our objects.

The 'Albedo' parameter is the color when light gets reflected; for now think of it as the material's main color.

###Textures

Textures are images that get stretched around meshes.

###Shaders

You want a good balance between frame rate and realism; use diffuse for faster frames, PBR for realism.

###Transforms

You can **transform** primitives by **position**, **scale**, and **rotation**.
Transforms can be nested, think like a robot arm with multiple joints (Unity calculates using **matricies**)
These transforms use a **left handed co-ordinate system**, meaning with your left hand, point up with your index finger and 90 degrees point right with your thumb.

* Y means up and down
* X means right and left
* Z means forward or backwards (depth)

Transforms are accessible in Unity in a 'Transform' window.

##Animations

Open up 'Window' > 'Animation' to see the tab for creating Animations. Just add a property (e.g. Rotation).

###Keyframes

Keyframes are points in your animation.

We don't have to animate every keyframe because we have **interpolation**, **smoothing**, and **averaging**. This means we can specify the first and last keyframe, then pick an **Animation Curve**.

* **Interpolation** uses an **Animation Curve**, meaning that a property changes over time
* Cheat Sheet for Easing Functions: http://easings.net/
* You can change the easing in Animation under 'Curves' and 'Dopesheet'

###Animator

Unity has an **Animator** system that allows you to specify states through a **Macanim State Machine** (e.g. player is alive, resting, dead)

* The state machine connects together all of our animation states
* **Animation Parameters** specify when one state can go to another state (e.g. can't have a dead player transitioning to jumping animation)
* We use **scripts** to trigger animations; in Unity you can drag and drop scripts to objects like the clock

##Cameras

Every scene needs at least a **main camera**.

* **Clear Flags** property is what the camera sees if there is no 3D object (think of it as the background scene); don't ever use **Don't Clear**, which uses the last frame's scene (no use for VR)
* A **Layer** is like a folder for how we organize putting in game objects; by default, there is a 'Default' layer. First 8 Layers are for the Unity Engine.
*  **Culling Mask** property tells the camera what layers of the scene should be rendered

###Depth

There are two ways for cameras to capture a 3D space and represent it onto a 2D screen. Unity uses two types of depths: **Perspective** and **Orthographic**.

* **Perspective** is like a pinhole camera, where we take into account that some objects are closer to the camera and should appear larger (like in real life)
* **Orthographic** renders objects based on their size and not based on how close they are

For VR, we want to use Perspective.

###Field of View

The **Field of View** is what your camera can capture; this is normally dependent on your VR headseat.
Depending on what you can view, we're interested in only rendering what we can see (for efficency) with **Clipping Planes** that specify rendering anything from **Near** to **Far**
With Clipping, it helps with optimization using a technique called **Frustrum Culling**

###Camera for VR

The neat thing about VR is that we're using two cameras, one for each eye.
We measure the distance between the two lenses on our VR system (e.g. Cardboard, Vive); usually done through API
VR allows you to track your head rotation; we do this with scripts

###Scripting Basics

MonoDevelop is the default program for scripts
Scripts by default are in C#
By default there are two methods: **Start** (initialization) and **Update** (called every frame)
A **Quaternion** is the name for stored gyro data
Google SDKs use more than just camera distance from each eye, it also uses the Gyro, Compass, and Accelerometer to combine this data into a **sensor fusion**; i.e. use the API for head tracking

##Lights

There are four types of lights in Unity:

* **Directional** is well, a strong source of light coming from a direction at a distance, like the sun.
* **Point** light emits a sphere of light from its current position, like a lightbulb.
* **Spot** light shoots out a cone of light, like a lighthouse or a flashlight.
* **Area** light have to be **baked**, meaning it needs to do some computation; think of it like a flood light in a stadium.

###Generic Light Properties

Generic light properties include:

* color
* intensity (0) for no effect, (8) for max
* shadows are fairly computationally expensive, especially on mobile. There are 3 types: none, hard (not realistic, but computationally easier), and soft (computationally intensive)
* render mode means should we always render these lights, which you normally leave Auto (basically choice of making people sick from dropped FPS or less realistic)
* culling mode says what layer should this light interact with

###Baking

We can 'cheat' by rendering static light sources ahead of type using **baking**, a way to compute advanced lighting effects like indirect light bounces and realistic shadows.
Remember that Unity requires a light to be **static** in order to use baking; this is just a property. You can have items be static for different things (lights, navigation, etc)
Click 'Bake' and it'll build (takes a few minutes)

###Light Panel

Access Lighting through 'Window' > 'Lighting'. You can then filter by Objects, Scene, and Lightmaps

###Global Illumination (GI)

**Global Illumination** is a system for how lights bounce off of surfaces onto other surfaces (indirect light) instead of just the light hitting a surface directly from a light source (direct light).

##Quality

You can change a few settings that can have a large impact on performance:

* Edit > Project > Quality Settings
* Edit > Project > Player Settings

#Scripting

Programming in Unity is done through C# or a variation of JavaScript. 

##Behaviors

Scripts describe behaviors that get attached to game objects. For example, a behavior might be:

* A dog object might have a 'bark' behavior
* A dog object might have a 'walk around' behavior

##Start and Update

Scripts have built in methods for handling calls at certain keyframes:

* `Start()` method is called only on initialization
* `Update()` method is called once per frame

###Time.deltaTime

An example call with [Time.deltaTime](docs.unity3d.com/ScriptReference/Time-deltaTime.html
) to make an object fall at 2.5 meters per second looks like this:

    void Update() {
        transform.Translate(0, -2.5f * Time.deltaTime, 0, Space.World);
    }

What's going on is that Update() is called every frame, but different hardware runs at different frames per second (e.g. 60, 90, 120).

We need Time.deltaTime to smooth out the animation so the animation can be **framerate-independent** (and change every frame, independent of hardware).

##Unity Documentation

Unity organizes the documentation into two sections, **Manual** and the **[Scripting]**(https://docs.unity3d.com/ScriptReference/).

###Scripting API

The **scripting API** uses C# and the game engine is divided into **namespaces** (a way to group classes togethers). Other namespaces outside of Unity are .NET's **System** namespace, used in commands like `System.Console.WriteLine("Hello World");` to call a class **Console**. The Unity API breaks down into:

* **UnityEngine** to access every object.
* **UnityEditor** to extend the Unity Editor.

####Unity Engine

Namespaces in the Unity Engine include things like:

* UnityEngine.VR has specific namespaces
* **Classes** is where most of your objects will be

You can click on the question mark icon in Unity to directly jump to the documentation. Make use of Search on the docs.

##Creating Objects

**Instantiating** is when we create a new instance of an object. One way to tell what object to create is by starting an empty script, then drag and drop to the editor. You can then control an object by **reference**.

Code example to make an object appear at specific coordinates (Vector3) with no rotation (the Quaternion part):

    using UnityEngine;
    using System.Collections;

    public class ObjectMaker : MonoBehaviour {
        public GameObject objectToCreate;
    
        void Start() {
            // Make an object
            Object.Instantiate(objectToCreate, new Vector3(2, 4, 6), Quaternion.identity);
        }
    } 

More links:

* [Object Instantiation](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html)
* [Vector3 is how you specify an object in 3D space (x, y, z)](https://docs.unity3d.com/ScriptReference/Vector3-ctor.html)
* [Quaternions are how to rotate an object in 3D, better than Euler Angles](http://blog.preoccupiedgames.com/quaternions-not-satan/)

