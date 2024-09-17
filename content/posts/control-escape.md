+++
title = 'Enhance Your macOS Productivity with Hammerspoon: Remap Caps Lock for Dual Functionality'
date = 2024-09-17T21:15:12+07:00
draft = false
+++

You reformat your computer, 100% clean. But your forgot which apps are crucial to you because they've always been running in the background.

This just happened to me, I always had this app called "Hammerspoon", but I forgot about it. I reformat my computer and I didn't install it back.

## What is Hammerspoon?

Hammerspoon is a powerful automation tool for macOS that allows you to script custom workflows, remap keys, and much more. As a developer, it’s been invaluable in improving my productivity, particularly with a feature that lets me remap the Caps Lock key to perform dual functions.

So this time, I resolve in documenting how this works.

## Install

```
brew install --cask hammerspoon
```

## Remap Caps Lock to Control

To remap the Caps Lock key to act as Control:

Go to System Preferences > Keyboard > Modifier Keys.
Select Caps Lock and choose Control from the dropdown menu.
For more details, check out [Apple's guide](https://support.apple.com/guide/mac-help/change-the-behavior-of-the-modifier-keys-mchlp1011/mac).

## Config

Run these commands to set up Hammerspoon for remapping Caps Lock:

```
mkdir -p ~/.hammerspoon/Spoons

git clone https://github.com/jasonrudolph/ControlEscape.spoon.git ~/.hammerspoon/Spoons/ControlEscape.spoon

cd ~/.hammerspoon/Spoons/ControlEscape.spoon

script/setup
```

So easy and improves so much my experience in mac as a developer.