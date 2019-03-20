---
layout: post
title: Macbook OSX Chunkwm
---


# {{ page.title }}

One of the things I really miss about being on Ubuntu is a tiling window manager like i3.

# Keyboard Preferences 

Instead of running `xset`, on a Mac you can just modify 'System Preferences' -> 'Keyboard' and
change the 'key repeat' to 'fast' and 'delay until repeat' to 'short'.

# Chunkwm and Skhd

Chunkwm and skhd seemed like a neat idea, but I personally found it buggy.

* __chunkwm__ is a tiling window manager
* __skhd__ is a hotkey daemon that we'll use to setup keyboard shortcuts

Install through brew, then run with:

* `brew services start skhd`
* `brew services start chunkwm`

## Configuration Files

Your chunkwm configuration file is in `~/.chunkwmrc`
Your skhd configuration file is in `~/.skhdrc`

## Modes

chunkwm supports three modes:

* __binary space partitioned__ (tiling) with: `alt + e`
* __monocle__ (full size windows stack on top of each other): `alt + s`
* make a single window __float__ with `shift + alt + space` on the focused window

## Commands

Setup fullscreen with `alt + f`
`alt` + `h`/`j`/`k`/`l` to move focus to the left, lower, upper, right windows
`alt` + `p` to make a window float
`alt` + `t` to make a window float and go to center of the screen
`cmd` + `h`/`j`/`k`/`l` to move window to the left, lower, upper, right
`alt` + `e` to toggle window type (from row to column)
`alt` + `d` to destroy window

## Commands (Different Windows)

`shift + alt - 1` send to desktop 1
`shift + alt - 2` send to desktop 2
`shift + alt - 3` send to desktop 3

# Amethyst

I personally like Amethyst with Fullscreen and Binary Space Partition mode.

https://github.com/ianyh/Amethyst

# BetterTouchTool

I like BetterTouchTool for remapping new keys and shortcuts to applications.

