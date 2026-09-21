---
title: "Getting Started Dueling Booting"
subtitle: ""
date: 2026-09-21T14:17:56Z
lastmod: 2026-09-21T14:17:56Z
draft: false
authors: [Gaz]
description: "Intro the the duel booting series of posts."
tags: ["duel-booting"]
categories: []
series: ["Duel-booting-adventures"]
featuredImage: "featured-image.png"
---

Welcome to a new series documenting my journey duel booting my main desktop, in this post I go over why I'm duel booting over just switching to just running linux and why I have made the some of the choices I have.

## My requirements

I use my desktop for lots of my hobbies, which for those that know me isn't a surprise, from gaming to learning music production my desktop PC is at the center of it all.

So as much as it pains me to admit I still need windows running for some of things I do so my requirements:

- I need a file system that can be accessible by operating systems
- I want something simple that's quick to set up in case I have to it again, I have enough complex work from my day job
- It needs to have a decent kernel for gaming performance and the driver support to back that up

## Choosing a distro

Given my requirements this was actually fairly easy choice, CachyOS easily fits the bill, native kernel level support for NTFS means I can share the same game binaries across linux (with the help of proton) and windows so no duplicates and less reboots to switch between Operating systems, the setup on all accounts I've read has been simple and the kernel optimizations for gaming have been difficult to beat plus they keep there own copies of stable nvidia drivers on their own repos.

## Some stuff I need to work out

Now I run a lot of hardware that originally only supported Windows or Mac which is one of the things that has held me back as even my trusty Scarlett 2I2 does not have a native linux driver, and last time I looked the goxlr on linux project was in it's infancy, so what so I need to look at?

- Getting my GoXLR up and running
- Getting my StreamDeck going with similar functionality to windows
- Getting steam to launch games stored on a NTFS formatted drive

The above list I will document of future posts, the next post I will go over some of the struggles I faced getting my existing setup ready for actually duel booting.
