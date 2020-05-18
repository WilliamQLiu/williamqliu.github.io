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

# WWDC Videos

* [Best Practices for Building SpriteKit Games](https://developer.apple.com/videos/play/wwdc2014/608/)
  Talks about scalability, game structure, performance best practices
* [Deeper into GameplayKit with DemoBots](https://developer.apple.com/videos/play/wwdc2015/609/)
  Sample code for a demo game using best practices
  [Documentation Archive for DemoBots](https://developer.apple.com/library/archive/samplecode/DemoBots/Introduction/Intro.html)
* [Introducing GameplayKit](https://developer.apple.com/videos/play/wwdc2015/608/)
  Provides common design patterns and architectures (works with SpriteKit, SceneKit, Metal)
  E.g. Entities & Components, State Machines, Agents, Pathfinding, MinMax AI, Random Sources, Rule Systems
* Can find additional videos under [Topics](https://developer.apple.com/videos/topics/) 'Games', '2D Graphics'

## Best Practices for Building Spritekit Games

### Introducing GameplayKit

The GameplayKit framework focuses on gameplay topics like AI, pathfinding, and autonomous movement (kinda like
how Apple has SpriteKit, SceneKit, and Metal for a visual framework). Think of GameplayKit as an API of
gameplay solutions with seven major features.

* Components - how to structure your game objects and game logic
* State machines - describes the statefulness and state changes of your game objects
* Agents - autonomously moving entities that are controlled by realistic behaviors and goals
* Pathfinding - navigation graph generation and how we move entities between the passable areas in the game world
* MinMax AI - give life to our computerr-controlled opponents
* Randomization - allow random sources and distributions
* Rule Systems - a way to model discreet and fuzzy logic

#### Entities and Components

Classic Problem - In games, there are many entities and components. Say we have a tower defense game
and there are objects of say towers and archers, where does `[shoot:]`, `[move:]`, `[isTargetable]` go?

    GameObject
      > Projectile
      > Tower
      > Archer

We can copy and paste code across archers and towers, but we now have duplicate code where you have to update.
Another option is to use an inheritance model so that a higher class (say GameObject), but that also means
shared functionality needs to move to a higher and higher hierarchies (to the point where a basic game object
is now complicated with layers of inheritance). So what are other options? Entities and Components!

__Components__ encapsulate singular elements of our game logic. Changing the above into components would
make it have loose coupling from the hierarchy.

    MoveComponent - deals with moving
    ShootComponent - deals with shooting
    TargetComponent - what it means to be targetable

When you create a new entity in the game, just look up what components you have available.
An example of this might be an archer that gets rooted (we can apply this affect by temporarily
removing the 'MoveComponent'). In SwiftKit, all components are a `GKComponent`.

__Entities__ are defined through `GKEntity`, the entity base class, and it has a collection of components
that you can add or remove dynamically.

Example might look like:

    /* Make our archer */
    GKEntity *archer = [GKEntity entity];

    /* Archers can move, shoot, be targeted */
    [archer addComponent: [MoveComponent component]];
    [archer addComponent: [ShootComponent component]];
    [archer addComponent: [TargetComponent component]];

    /* Create MoveComponentSystem */
    GKComponentSystem *moveSystem = [GKComponentSystem systemWithComponentClass:MoveComponent.class];

    /* Add archer's MoveComponent to the System */
    [moveSystem addComponent: [archer componentForClass:MoveComponent.class]]

#### State Machines

__State Machines__ are the backbone of many gameplay elements like Animation, AI, UI, levels, etc.
An example of a state machine might be PacMan, where we have:

    Chase <-> Flee
    Respawn
    Defeated

Another example might be animations with:

    IdleAnimation <-> MoveAnimation
    AttackAnimation

Or a game user interface using states to show what UI elements to show and what other game elements are running:

    Menu
    Playing
    Paused
    GameOverr

Different types of state machines include:

__GKStateMachine__
`GKStateMachine` is the general purpose finite state machine, which can only have a single current state, but
can have all possible states too. `[enterState` causes state transition and checks if the transition is valid.
Calls `[exit]` on previous, `[enter]` on next state and updates `currentState`

__GKState__
`GKState` is an abstract class that implements logic in Enter/Exit/Update. You would subclass GKState to define
each state and the rules for allowed transitions between states. An instance of `GKStateMachine` would be used
to manage a machine that combines several states. The idea behind this system is that it provides a way to organize
code in your game by organizing state-dependent actions into methods that run when entering a state, exiting a
state, and periodically while in a state (e.g. animation frame).

#### Agents, Behaviors, and Goals

So what does agents, behaviors, and goals solve? Games need believable movements (e.g. walk around a square building,
you don't do sharp 90 degree turns when bumped into object)

__Agents__ are a component that moves a game entity according to a set of goals and realistic constraints
Class is `GKAgent` that is really just a `GKComponent`.

* Driven by behaviors and goals
* Realistic constraints

__Behaviors__ are made up of a group of goals that influence the movement of an agent
Class is `GKBehavior`, a dictionary-like container of goals wheer you can add/remove goals, adjust weights of goals, etc.

* Goals combined via weights

__Goals__ are an influence that motivates the movement of one or more agents. Class is `GKGoal`.

* Examples can be 'Seek', 'Avoid', 'Flee', 'Follow Path', 'Wander', 'Intercept', 'Separate', etc.

Sample Code for Agents, Behaviors, and Goals

    /* Make some goals, we want to seek the enemy, avoid obstacles, target speed */
    GKGoal *seek = [GKGoal goalToSeekAgent:enemyAgent];
    GKGoal *avoid = [GKGoal goalToAvoidObstacles:obstacles];
    GKGoal *targetSpeed = [GKGoal goalToReachTargetSpeed:50.0f];

    /* Combine goals into behavior */
    GKBehavior *behavior = [GKBehavior behaviorWithGoals:@[seek,avoid,targetSpeed] andWeights:@[@1.0,@5.0,@0.5]];

    /* Make an agent - add the behavior to it */
    GKAgent2D *agent = [[GKAgent2D* alloc] init];
    agent.behavior = behavior;

__AgentDelegate__ is a __protocol__ that synchronizes the state of an agent with its visual representation in
your game; defined through `GKAgentDelegate`. Allows you to sync graphics, animations, physics with two callbacks:
`[agentWillUpdate:]` called before updates and `[agentDidUpdate:]` called after updates for say
a SpriteKit Node, a SceneKit Node, or a Render Component. Sample code looks like:

    @implementation MyAgentSpriteNode

    (void)agentWillUpdate:(GKAgent2D *)agent {

        /* Position the agent to match our sprite */
        agent.position = self.position;
        agent.rotation = self.zRotation;
    }

    (void)agentDidUpdate:(GKAgent2D *)agent {

        /* Update the sprite's position to match the agent */
        self.position = agent.position;
        self.zRotation = agent.rotation;
    }

So why use this? For say a space shooter game, you can have projectiles attack your ship with smart pathing
based off the goals that are already predefined.

#### Pathfinding

__Pathfinding__ is an issue in many games. Let's first define a few things:

* __Pathfinding__ operations on a navigatio __graph__
* __Graphs__ are collections of __nodes__
* __Nodes__ are joined by __connections__
* __Connections__ are __directional__ (whether single or bidirectional)
* __Optimal path__ exists between any two connected nodes

##### __GKGraph__

`GKGraph` is the abstract graph base class. It is the container of graph nodes and dynamically allows you to
add or remove nodes, connect new nodes, find paths between nodes. There are two specializations of the `GKGraph`,
which are:

* Grid Graphs
* Obstacle Graphs

##### __Grid Graphs__

A `GKGraph` that works with 2D grids as class `GKGridGraph`.

* Creates nodes on the grid
* Cardinal connections
* Optional diagonal connections
* Easily add/removal of grid nodes

##### __Obstacle Graphs__

A `GKGraph` that works with pathing around obstacles as class `GKObstacleGraph`.

* Obstacles are arbitrary polygons
* Dynamically add or remove obstacles
* Dynamically connect nodes
* Obstacles can have a buffer radius ('safety zone' around obstacles)

Sample code looks like:

    /* Make an obstacle - a simple square */
    vector_float2 points[] = {{400,400}, {500,400}, {500,500}, {400,500}};
    GKPolygonObstacle *obstacle = [[GKPolygonObstacle alloc] initWithPoints:points count:4];

    /* Make an obstacle graph */
    GKObstacleGraph *graph = [GKObstacleGraph graphWithObstacles:@[obstacle] bufferRadius:10.0f];

    /* Make nodes for hero position and destination */
    GKGraphNode2D *startNode = [GKGraphNode2D nodeWithPoint:her.position];
    GKGraphNode2D *endNode = [GKGraphNode2D nodeWithPoint:goalPosition];

    /* Connect start and end node to graph */
    [graph connectNodeUsingObstacles:startNode];
    [graph connectNodeUsingObstacles:endNode];

    /* Find path from start to end */
    NSArray *path = [graph findPathFromNode:startNode toNode:endNode];

##### __GKGraphNode__

For advanced pathfinding, consider using `GKGraphNode`, the graph node base class. Use this if you need
more advanced features (e.g. terrain type of mountains costs twice as much vs flat ground) so that you are
not just using the shortest path. Or say you have portals that are a shortcut between places.

__SpriteKit Integration__

You can easily generate obstacles from SKNode bounds, physics bodies, or textures.

    /* Makes obstacles from sprite textures */
    (NSArray*)obstaclesFromSpriteTextures:(NSArray*)sprites accuracy:(float)accuracy;

    /* Makes obstacles from node bounds */
    (NSArray*)obstaclesFromNodeBounds:(NSArray*)nodes;

    /* Makes obstacles from node physics bodies */
    (NSArray*)obstaclesFromNodePhysicsBodies:(NSArray*)nodes;

#### MinMax AI

__MinMax AI__ looks at all player moves, builds a decision tree, and maximizes potential gain while minimizing
potential loss. An example of this is say tic-tac toe. You can use this for AI-controlled opponents or
for suggesting moves for the human player. You might see this in say turn-based games (or any game with discrete moves).
You can adjust the difficulty (e.g. how far the AI looks ahead or selecting suboptimal moves).

##### __GKGameModel__ protocol

The `GKGameModel` protocol the an abstraction fo the current game state. For a chess game, you might have:

* a list of players
* current active player
* player scores
* possible player moves

##### __GKGameModelUpdate__ protocol

The `GKGameModelUpdate` protocol is an abstraction of a game move; implement this to describe a move in your
turn-based game so that a `GKStrategist` object can plan game moves.

* Used by MinMax to build the decision tree
* Apply to `GKGameModel` to change state

##### __GKGameModelPlayer__ protocol

The `GKGameModelPlayer` is an abstraction of a player; implement this to describe a player in your
turn-based game so that a `GKStrategist` object can plan game moves.

* Players make moves via `GKGameModelUpdate`

##### __GKMinmaxStrategist__ class

The `GKMinmaxStrategist` class is an AI that chooses moves in turn-based games using a __deterministic__ strategy.

##### __GKMonteCarloStrategist__ class

The `GKMonteCarloStrategist` class is an AI that chooses moves in turn-based games using a __probabilistic__ strategy.

Example Code

    /* ChessGameModel implements GKGameModel */
    ChessGameModel *chessGameModel = [ChessGameModel new];
    GKMinmaxStrategist *minmax = [GKMinmaxStrategist new];

    minmax.gameModel = chessGameModel;
    minmax.maxLookAheadDepth = 6;

    /* Find the best move for the active player */
    ChessGameUpdate *chessGameUpdate = [minmax bestMoveForPlayer:chessGameModel.activePlayer];

    /* Apply update to the game model */
    [chessGameModel applyGameModelUpdate:chessGameUpdate];

#### Random Sources

We have `rand()`, but games have unique random number needs. We need:

* Platform-independent determinism
* Multiple Sources
* Number distribution

Features include:

* deterministic
* serializable
* industry-standard algorithms
* random distributions (true random, gaussian, anti-clustering)
* NSArray shuffling (e.g. shuffle a deck of cards)

__GKRandomSource__ class

`GKRandomSource` is the base class for random sources.

* Guaranteed determinism with same seed

__GKRandomDistribution__ class

`GKRandomDistribution` is the base class for random distribution; purely random.

__GKRandomDistribution__ class

`GKGaussianDistribution` is a "bell curve" distribution (leans more towards the mean)

__GKShuffledDistribution__ class

`GKShuffledDistribution` is an anti-clustering distribution that reduces or eliminates 'runs'.

Code examples (simple usage):

    /* Create a six-sided dice with its own random source */
    let d6 = GKRandomDistribution.d6()

    /* Get die value between 1 and 6 */
    let choice = d6.nextInt()

Code examples (custom die):

    /* Create a custom 256-sided die with its own random source */
    let d256 = GKRandomDistribution.die(lowest:1, highest:256)

    /* Get die value between 1 and 256 */
    let choice = d256.nextInt()

Code examples (array shuffling)

    /* Make a deck of cards */
    var deck = [Ace, King, Queen, Jack, Ten]

    /* Shuffle them */
    deck = GKRandomSource.sharedRandom().shuffle(deck)
    /* Possible result - [Jack, King, Ten, Queen, Ace] */

    /* Get a random card from the deck */
    let card = deck[0]

#### Rule Systems

Games consist of three elements:

* __Nouns__ (Properties) - the position, speed, health, equipment, etc.
* __Verbs__ (Actions) - run, jump, use item, accelerate, etc.
* __Rules__ - how your nouns and verbs interact

What is a __Rule System__?

Say you have a role-playing game with turn-based combat that include rules governing what happens when
opposing characters move into the same space. You might have to calculate say an attack of opportunity for
the monster that just landed next to your hero. These rules can quickly get complex with conditional logic statements.
So what happens is that we have these systems with __emergent behavior__, cases where the interactions
between simple entities follow simple rules, but lead to interesting patterns in the system as a whole.

Rule systems provide an abstraction that treats certain elements of game logic as data, decompose your game into
functional, reusable, and extensible pieces. By incorporating fuzzy logic, rule systems can treat decisions
as continuous variables instead of discrete states, leading to complex behavior from even simple combinations of rules.

Another example is binary driver AI

* Input is distance
* Output is either [slowDown] or [speedUp], but we might have some weird spots around the cutoff of say 5
  where the AI doesn't know whether to speed up or slow down, resulting in jerky movements
* With fuzzy logic, you don't have to be either slowDown or speedUp, can be inbetween (grades of true)
* Fuzzy logic deals with approximations

We separate what we should do from how we should do it

* state facts about the world
* take deferred actions based on the facts of the world

We do this by designing a rule system, which indlues two main classes:

* `GKRule` to represent a specific decision to be made based on external state
* `GKRuleSystem` which evaluates a set of rules against state data to determine a set of facts

__GKRule__ class

A `GKRule` is made up of two parts, a boolean __predicate__ and an __action__.
A rule's __predicate__ is the decision-making part; it evaluates a set of state information and returns a Boolean result.
A rule's __action__ is code whose execution is triggered only when the predicate produces a true result.
GameplayKit evaluates rules in the context of the rule system. Typically, a rule's predicate tests state data
maintained by the rule system and its action either changes the system's state or asserts a fact.
A rule does not need local state data; this allows you to design individual rules as functional units with well-defined
behavior and reuse them in any rule system.

__GKRuleSystem__ class

A `GKRuleSystem` has three key parts: an __agenda of rules__, __state data__, and __facts__.
The __agenda__ is where you combine a set of rules by adding them to the __agenda__ of a rule system object with
(`addRule:` or `addRulesFromArray:` methods). By default, rules are evaluated based on order (or change `salience` property).
The __state data__ is the rule system's `state` dictionary, which contains information rules can be tested against.
The state dictionary can reference anything useful to your set of rules, including strings, numbers, or other classes.
The __facts__ represent a conclusion drawn from the evaluation of rules in a system and can be any type of object.
When a rule system evaluates its rules, the rule actions can _assert_ a fact or _retract_ a fact. Facts can vary
in grade to define some __fuzzy logic__. For example, a character moving to a space might not always trigger
an attack; you might instead trigger an attack only when the character moving is also in a vulnerable state.

For details, see: https://developer.apple.com/library/archive/documentation/General/Conceptual/GameplayKit_Guide/RuleSystems.html

Example Code:

    /* Make a rule system */
    GKRuleSystem* sys = [[GKRuleSystem alloc] init];

    /* Getting distance and asserting facts */
    float distance = sys.state[@"distance"];
    [sys assertFact:@"close" grade:1.0f - distance / kBrakingDistance];
    [sys assertFact:@"far" grade:distance / kBrakingDistance];

    /* Grade our facts - farness and closeness */
    float farness = [sys gradeForFact@"far"]
    float closeness = [sys gradeForFact@"close"];

    /* Derive Fuzzy acceleration */
    float fuzzyAcceleration = farness - closeness;
    [car applyAcceleration:fuzzyAcceleration withDeltaTime:seconds];

# Deeper into GameplayKit with DemoBots

The tools and technology used for DemoBots include:

## ActionEditor

There are a lot of animation states for our bots. To keep these textures to a minimum,
we can create some of the animations as Actions. For example, a zap action is a reference action.
You create the action once, then can apply to multiple bots.

## Assets Catalog

Depending on the size of your device (e.g. ipad has more pixels, iphone has less pixels), you can
specify different assets depending on device. As the scene height scales, the player height scales.

## SKCameraNode

The camera is a node in the scence (and has a position); this allows you to move the camera and
change its position. You can add constraints (like following the hero). You can also add more constraints
like if you are approaching the corner of the screen (when you get close to an edge, the camera stops
following the hero).

## GKStateMachine

The PlayerBot state has the following states using a `GKStateMachine`:

* Appear
* Player Controlled
* Hit
* Recharging

We also use states for the game state, e.g.

* Game is Paused (e.g. menu)
* Level is completed
* Game is Running

## GKEntity and GKComponent



# Sample Code

https://developer.apple.com/documentation/gameplaykit

* Boxes: GameplayKit Entity-Component Basics
* Dispenser: GameplayKit State Machine Basics
* Pathfinder: GameplayKit Pathfinding Basics
* AgentsCatalog: Using the Agents System in GameplayKit
* FourInARow: Using the GameplayKit Minmax strategist for Opponent AI
* DemoBots: Building a Cross Platform Game with SpriteKit and GameplayKit

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

