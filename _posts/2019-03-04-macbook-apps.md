---
layout: post
title: Macbook OSX Chunkwm
---


# {{ page.title }}

One of the things I really miss about being on Ubuntu is a tiling window manager like i3.

# Homebrew

Install homebrew so you can run `brew services` and install packages/programs correctly.

It's also useful if you want to install say different versions of a program (e.g. Go).

    $brew info go
    go: stable 1.13.1 (bottled), HEAD
    Open source programming language to build simple/reliable/efficient software
    https://golang.org
    /usr/local/Cellar/go/1.13.1 (9,268 files, 414.0MB) *
      Poured from bottle on 2019-10-04 at 14:47:19
    From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/go.rb
    ==> Requirements
    Required: macOS >= 10.10 ✔
    ==> Options
    --HEAD
        Install HEAD version
    ==> Analytics
    install: 122,188 (30 days), 326,442 (90 days), 1,220,770 (365 days)
    install_on_request: 97,701 (30 days), 256,432 (90 days), 920,590 (365 days)
    build_error: 0 (30 days)

    $go version
    go version go1.13.1 darwin/amd64

    $brew install go@1.12

    $cd /usr/local/Cellar/
    # you'll see go and go@1.12

    $brew unlink go
    $brew link go@1.12 --force
    $go version
    go version go1.12.10 darwin/amd64

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

# Spectacle

Simple Tiling

# Iterm2

I change some of the keymappings for iterm2 so that:

* cmd + shift + h = Previous Tab
* cmd + shift + l = Next Tab
* cmd + j = Select Split Pane Below
* cmd + k = Select Split Pane Above
* cmd + h = Select Split Pane Left
* cmd + l = Select Split Pane Right
* cmd + Return = Split Horizontally with Profile

