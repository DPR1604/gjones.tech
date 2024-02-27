---
title: "An Introduction to Jenkins"
subtitle: "The future of my CI and CD pipelines"
date: 2024-02-27T18:30:23Z
lastmod: 2024-02-27T18:30:23Z
draft: true
authors: ["Gaz Jones"]
description: "This post will be discussing the recent move to Jenkins"
tags: ["self-hosting", "automation", "jenkins"]
categories: ["self-hosting", "automation", "jenkins"]
series: ["Jenkins-ci", "Migration to github"]
resources:
- name: "featured-image"
  src: "featured-image.png"
---

In this post I will be discussing my recent move to using Jenkins for CI CD pipelines, this is the first post in this series covering my process on migrating the pipelines from the pipelines but these posts are also part of the migrating to github series.

### Why?

So why Jenkins? Well simply put I wanted to know how it works better, Jenkins is one of the tools I have used professtionally a few times however each time I have another team has managed the environment, this has restricted a number of feature I've been exposed to, this fact has made it difficult to suggest improvements as I don't know what is possible.

### The Setup

I made the descision to host jenkins at home I already have the ability to host VM's at home so there is no additonal host to me and as it's not something being constantly used this makes sense, the VM is only small put does the job the only thing likly to change in the near future is adding an actual agent.

I'm running Jenkins with the built in runners at the moment, Jenkins recommneds against this however all my pipelines use Docker containers to maintain a sterile environment between different pipelines so the considerations for having agent nodes don't apply to my current setup.

I run Jenkins natively on Alma Linux 8 I used the offical documentation as a guide to install it so I wont bother rehashing the process in this post but you can check it out [here](https://www.jenkins.io/doc/book/installing/linux/).

I have most of the common plugins from common setup, I'll discuss the plugins I use in more details in future posts.

This post is only the begining on my adventure into Jenkins I will be keeping up with documenting the process as I go through it.