---
layout: post
title: Swift Spritekit
---


# {{ page.title }}

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


