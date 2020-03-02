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

### Nodes

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


