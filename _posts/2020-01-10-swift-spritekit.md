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

## WWDC Videos

* [Best Practices for Building SpriteKit Games](https://developer.apple.com/videos/play/wwdc2014/608/)
  Talks about scalability, game structure, performance best practices
* [Deeper into GameplayKit with DemoBots](https://developer.apple.com/videos/play/wwdc2015/609/)
  Sample code for a demo game using best practices
  [Documentation Archive for DemoBots](https://developer.apple.com/library/archive/samplecode/DemoBots/Introduction/Intro.html)
* [Introducing GameplayKit](https://developer.apple.com/videos/play/wwdc2015/608/)
  Provides common design patterns and architectures (works with SpriteKit, SceneKit, Metal)
  E.g. Entities & Components, State Machines, Agents, Pathfinding, MinMax AI, Random Sources, Rule Systems
* Can find additional videos under [Topics](https://developer.apple.com/videos/topics/) 'Games', '2D Graphics'

### Introducing GameplayKit

__Entities and Components__

Classic Problem - given these objects, where does `[shoot:]`, `[move:]`, `[isTargetable]` go?

    GameObject
      > Projectile
      > Tower
      > Archer

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

### How do I start creating a Game

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

https://developer.apple.com/documentation/spritekit/skscene

### GameScene.sks (access GameScene through GUI)

The `GameScene.sks` file allows you to modify your `SKScene` through a GUI. You can:

* Select the 'Size' of the Scene (e.g. iPhone SE)
* Change the background color
* Change the `Anchor Point` (e.g. reference for all your assets). E.g. anchor point 0,0 is bottom left
* Change the 'Gravity' for the physics engine (X=0, Y=-9.8) is standard earth)
* On the bottom right you can see all your `Assets` that you dragged in from above
* Drag assets over and they appear as `SKSpriteNode` in your scene (and can then modify sprite sizes, textures, etc.
* You can animate your 'SKSpriteNodes' by adding `Actions`. E.g. `Move Action` on the sprite node that looks like a cloud
  The 'Action' can be found in your 'Object Library' (where you click on the '+' sign)

### GameScene.swift (access GameScene through Code)

In `GameScene.swift`, we are able to access the `GameScene.sks` through code (e.g. access Sprites through code).
You will see your `class GameScene: SKScene` with functions like `update` (called before each frame is rendered)
and you can do your custom scene setup (e.g. look at `self.childNode(withName: ...) as? SKSpriteNode ... `

Important functions that you might want in code might be movement functions like:

* `func touchDown`
* `func touchUp`
* `func touchMoved`

### The Rendering Loop

The rendering loop is tied to the `SKScene` object. Rendering runs only when the
scene is presented. SpriteKit only renders the scene when something changed so its efficient.
Here's what goes on in the rendering loop each frame:

* `update` - make changes to nodes, evaluate nodes
* `SKScene` evaluates actions
* `didEvaluateActions` -
* `SKScene` - simulates physics
* `didSimulatePhysics` -
* `SKScene` - applies constraints
* `didApplyConstraints`
* `didFinishUpdate`
* `SKView` renders the scene

## SKAction

__SKAction__ is an animation that is executed by a node in the scene.
Actions are used to change a node in some way (e.g. its position over time, its size
or scaling, its visibility, or node's contents so that it animates over a series of textures).

SKActions can be combined together either as a __group__ (multiple actions at once)
or as a __sequence__ (actions one after another). You can also repeat actions.

## Nodes

Nodes include:

* Base Nodes - a reference, point of view, foundation to all things displayed in a scene
* Nodes that Draw - display images, shapes, particles, text, video, tiles, or 3D content
* Nodes for Environmental Effects - environmental effects like audio, lighting, or areas with physics characteristics
* Nodes that Modify Drawing - modify the rendering of child nodes by cropping, applying Core Image filters, or 3D transforms

### SKEmitterNode

To add particles, you can use `SKEmitterNode` to create smoke, fire, sparks, and other particle effects.
Keep in mind that this is privately owned by SpriteKit so you cannot modify this class.
Important settings for this particle emitter include:

* birthrate - how many particles to create
* lifeline - how long to keep particles around
* position range - how much particle emitter varies in the x and y direction's start position
* angle - where particles can emit to
* speed - how fast to emit particles
* acceleration - how fast to accelerate particles

## GameplayKit

When you `import GameplayKit`, you have access to a lot of useful pre-built functions like:

* `GKRandomSource.sharedRandom().nextInt(upperBound: 6)`

## GameViewController

Sets up your Scene

## Physics

A __physics world__ is the simulation space for running physics calculations; one is setup by default.
In SpriteKit, you can associate a shape with each sprite for collision detection purposes; this is a __physics body__.
One of the properties that you can set on a physics body is a __category__, which is a bitmask indicating the
group or groups it belongs to. Example categories could be one for projectiles and one for monsters. When two
physics bodies collide, you can tell what kind of sprite you're dealing with by looking at its category.
You can set a __contact delegate__ to be notified when two physics bodies collide (and what to do if they are
the categories you want).

* Physics world can be created with an `SKPhysicsWorld`, which is the driver of the physics engine in a scene.
It allows you to configure and query the physics system.
* Physics bodies can be created with a `SKPhysicsBody`, which adds physics simulation to a node.

### SKPhysicsBody

You can add an `SKPhysicsBody` to add physics simulation to a node. The physics body must be associated with a node
object before you can apply forces or impulses to it.

    # Apply SKPhysicsBody
    sprite = self.childNode(withName: "sprite") as! SKSpriteNode
    sprite.physicsBody = SKPhysicsBody(rectangleOf: sprite.size)

    # Apply impulse to a sprite body
    sprite.physicsBody?.applyImpulse(CGVector(dx: 50, dy: 0))

Types of Physics Bodies:

* __dynamic__: A physical object with volume and mass (if an object needs to move around and collide with other objects)
* __static__: Unaffected by forces or collisions (i.e. take up space like a wall in a maze, but won't collide)
* __edge__: static volume-less body

This can be accessed through code with:

    sprite.physicsBody?.isDynamic = true

A body's physical properties also include:

* __mass__ - mass of the body in kilograms
* __density__ - the density of the object in kilograms per square meter
* __area__ - the area covered by the body
* __friction__ - the roughness of the surface of the physics body

How does this all look in code?

    let spaceShipTexture = SKTexture(imageNamed: "spaceShip.png")

    // Spaceship 1 with circular physics body
    let circularSpaceShip = SKSpriteNode(texture: spaceShipTexture)
    circularSpaceShip.physicsBody = SKPhysicsBody(circleOfRadius: max(circularSpaceShip.size.width / 2, circularSpaceShip.size.height / 2))

    // Spaceship 2 with rectangular physics body
    let rectangularSpaceShip = SKSpriteNode(texture: spaceShipTexture)
    rectangularSpaceShip.physicsBody = SKPhysicsBody(rectangleOf: CGSize(rectangularSpaceShip.size.width, rectangularSpaceShip.size.height))

    // Spaceship 3 with polygonal physics body (using Paths)
    let polygonalSpaceShip = SKSpriteNode(texture: spaceShipTexture)
    let path = CGMutablePath()
    path.addLines(between: [CGPoint(x: -5, y: 37), GGPoint(x: 5, y:37), ....])
    path.closeSubpath()
    polygonalSpaceShip.physicsBody = SKPhysicsBody(polygonFrom: path)

    // Spaceship 4 with using texture's alpha channel
    let texturedSpaceShip = SKSpriteNode(texture: spaceShipTexture)
    texturedSpaceShip.physicsBody = SKPhysicsBody(
        texture: spaceShipTexture,
        size: CGSize(width: texturedSpaceShip.size.width,
                     height: texturedSpaceShip.size.height))

### Bitmasks and Collisions

A __bitmask__ is data used for __bitwise operations__. You use a __mask__ (multiple bits in a byte)
that can be set either on or off in a bitwise operation. Some of the types of bitmasks we can use are:

* __Category Bitmask__ - which category do I belong to? e.g. a Player or Enemy
* __Collision Bitmask__ - with whom do I collide?
* __Contact Test Bitmask__ - I notify the system when I collide with ... a Player or an Enemy

Say we have a bitmask of 1 (`0x00000001`). Here is a bitwise shift to the left.

    0x00000001 << 1 = 0x00000010  # 1 * 2^1 = 2
    0x00000001 << 2 = 0x00000100  # 1 & 2^2 = 4

So how does this work in a real example?

    # Ball hitting grass
    ballSprite.physicsBody.collisionBitMask = 0x00000001
    grassSprite.physicsBody.categoryBitMask = 0x00000001

SpriteKit does a calculation for collision and does `0x00000001 AND 0x00000001 = 1` so there is collision.

### Collision

Once the ball actually hits the grass, we can take an action/do something:

* Assign the 'Contact Test Bitmask'
* To detect collision, adopt the class `SKPhysicsContactDelegate` and assign it to our Physics World
* Once a collision happens, `didBegin(_:)` function is called (thanks to `SKPhysicsContactDelegate`)

What happens is that:

    // Adopt the class SKPhysicsContactDelegate
    class GameScene: SKScene, SKPhysicsContactDelegate {
        ...

        let redBallCategory:UInt32  = 0x00000001 << 0  // 1
        let blueBallCategory:UInt32 = 0x00000001 << 1  // 2
        let groundCategory:UInt32   = 0x00000001 << 2  // 4

        # this function is called when collision happens
        func didBegin( contact: SKPhysicsContact) {

            # bitwise OR operation
            let collision: UInt32 = contact.bodyA.categoryBitMask | contact.bodyB.categoryBitMask  # e.g. 5

            let collision == groundCategory | redBallCategory {  // e.g. 5
                print("Contact")
            }
        }
    }

### CGPoint

A `CGPoint` is a structure that contains a point in a two-dimensional coordinate system.

### CGVector

A `CGVector` is a vector that specifies the gravitational acceleration applied to physics bodies in the physics world.

