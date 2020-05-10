---
layout: post
title: Swift Spritekit
---


# {{ page.title }}

The SpriteKit Framework is a general-purpose framework for drawing shapes, particles, text, images, and video
in two dimensions. SpriteKit offers a simple programming interface to make graphics-intensive 2 dimensional games.
SpriteKit works on iOS, macOS, tvOS, and watchOS, as well as integrates well with frameworks like GameplayKit
and SceneKit.

## Overview

https://developer.apple.com/documentation/spritekit

Documentation topics include:

* Essentials - Draw SpriteKit Content in a View, organize SpriteKit content into a SKScene, create layout of scene using Nodes
* Scene Renderers - Draw a SpriteKit scene using a rendering object (to drive the scene update cycle)
* Textures - Load graphics from various sources or use an atlas to maximize rendering performance
* Animation - Animate nodes with Actions (that change its structure or content)
* Constraints - Constrain the position or orientation of nodes
* Mathematical Tools - Used to facilitate other graphical work
* Physics Simulation - Add physics behaviors to nodes in your scene
* Physics Joints - Connect physics bodies by using conceptual tools like pins, sliding joints, and sprint joints
* Tiling - Configure the images or autotiling behavior of a tile map node
* Shaders - Customize node drawing by augmenting the node's color or shape
* Warping - Distort a node by supplying verticies and their transformations

## Simulator and Building on an iPhone

You can use the built-in simulator to simulate a phone or you can build directly to your phone.
Just plug in your phone to your computer's usb and select your iPhone during the build in Xcode.


* In Xcode, under Preferences > Account, you should add your AppleID and assign the project to a team
* If you get 'Untrusted Developer' when launching your app on your phone, then open the 'Settings' > 'General' > 'Device Management' > Choose your profile > 'Trust myprofile'

## Important Classes

* `SKView` - an object that displays SpriteKit content - this content is provided by an `SKScene` object
  e.g. look at the Interface Builder, this is just a view that holds a scene object
* `SKScene` - the root node for all SpriteKit objects displayed in a view
  e.g. you place background images, all your nodes, etc.
    * the size of the scene defines the visible area
    * nodes can be placed outside of the visible portion of the scene, but will still be processed by the scene
      (and will be ignored by the renderer)
    * contents are scaled to fit the view
    * anchor points position the coordinate system (e.g. can be bottom left 0,0 center 0.5,0.5, or top right 1,1)
* `SKNode` - provides baseline behavior (does not actually draw)
    * provides a coordinate system to its children (e.g. everything is drawn using SKNode subclasses)
    * can be used to represent layers
    * can be used as a hidden trigger
* `SKSpriteNode` - a subclass of `SKNode`; the basic building blocks of your game
    * Can be textured OR colored and untextured
    * E.g. 'Sky' being the root node (z=0)
            |_ Missiles (z=-1)
            |_ Body (z=100)
            |_ Rotor1 (z=+1)
            |_ Rotor2 (z=+1)
* `SKAction` - an animation that is executed by a node in the scene (i.e. change a node in some way like move its
  position over time, an animation that fades out an object)
* `SKPhysicsBody` - add physics simulation to the node

### How do these all fit together?

SKView
  SKScene (the root node)
    SKNode
      - SKSpriteNode1
      - SKSpriteNode2
      - SKSpriteNode3

### How do I create a Game

You can create a 'Single Page Application' or a 'Game'. Select a 'Game' and then pick 'SpriteKit' so that it
will prefill in some defaults.

A SPA will get you a:

* AppDelegate.swift (same as Game)
* ViewController.swift (instead of GameViewController.swift, which is just using a View with the `SKView` class)
* Main.storyboard (same as Game)
* Assets.xcassets (same as Game)
* LaunchScreen.storyboard (same as Game)
* info.plist

With a SPA, you will need to add in:

* GameScene.sks (a game scene, which is a subclass of `SKScene`)
* ViewController.swift / GameViewController.swift (connect the `GameScene.sks` with the `GameScene.swift`)

## Assets

Under `Assets.xcassets`, you can place your images, music, etc.

## GameScene

The `GameScene.sks` file allows you to modify your `SKScene`. You can:

* Select the 'Size' of the Scene (e.g. iPhone SE)
* Change the background color
* Change the `Anchor Point` (e.g. reference for all your assets). E.g. anchor point 0,0 is bottom left
* Change the 'Gravity' for the physics engine (X=0, Y=-9.8) is standard earth)
* On the bottom right you can see all your `Assets` that you dragged in from above
* Drag assets over and they appear as `SKSpriteNode` in your scene (and can then modify sprite sizes, textures, etc.
* You can animate your 'SKSpriteNodes' by adding `Actions`. E.g. `Move Action` on the sprite node that looks like a cloud
  The 'Action' can be found in your 'Object Library' (where you click on the '+' sign)

## Nodes

Nodes include:

* Base Nodes - a reference, point of view, foundation to all things displayed in a scene
* Nodes that Draw - display images, shapes, particles, text, video, tiles, or 3D content
* Nodes for Environmental Effects - environmental effects like audio, lighting, or areas with physics characteristics
* Nodes that Modify Drawing - modify the rendering of child nodes by cropping, applying Core Image filters, or 3D transforms

## GameViewController

Sets up your Scene

## GameScene

Logic for your game

## Physics

A __physics world__ is the simulation space for running physics calculations; one is setup by default.
In SpriteKit, you can associate a shape with each sprite for collision detection purposes; this is a __physics body__.
One of the properties that you can set on a physics body is a __category__, which is a bitmask indicating the
group or groups it belongs to. Example categories could be one for projectiles and one for monsters. When two
physics bodies collide, you can tell what kind of sprite you're dealing with by looking at its category.
You can set a __contact delegate__ to be notified when two physics bodies collide (and what to do if they are
the categories you want).


