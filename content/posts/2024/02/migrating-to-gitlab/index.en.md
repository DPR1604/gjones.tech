---
title: "Migrating to Github"
subtitle: "Why I've decided to stop self-hosting git"
date: 2024-02-20T17:10:53Z
lastmod: 2024-02-20T17:30:53Z
draft: false
authors: [Gaz]
description: ""
tags: ["self-hosting", "automation"]
categories: ["git", "self-hosting"]
series: ["Migration to github"]
resources:
- name: "featured-image"
  src: "featured-image.png"
---

In this post I will discussing the reasons for my recent migration to github from gitlab and the initial process I took to move my repositories over to github this is a the first post in the series as I document the additional steps of using a mixture of github actions and Jenkins to build brand new pipelines with the devops knowledge I have built up.

### Why?
It's important to note throughout this series when I mention Gitlab I am specifically talking about Gitlab enterprise edition self-hosted which I was doing at no cost utilizing oracle cloud's always free tier.

So what made me want to move, well after 3 years of using gitlab and several migrations between providers for servers later I kept running into issues with connectivity to my gitlab server and this wasnt the first time I had experienced a problem caused by a gitlab update, now this isnt an issue with Gitlab as a whole I had more issues with my personal one then any instance I have used for work and if i'm honest it's probably because I was hosting it on an arm server, but the free tier kept my costs down and that was my main consideration.

I was putting a significant amount of time with the upkeep of the gitlab server, more then any other as I didnt want to put my trust into automatic updates given what I had seen happen in the past with manual updates causing the server to not come back up for a long time (again low resource arm server to blame).

### Weighing options
So I set out looking at other offerings, something I used to do a lot in my early career was spinning up something new using it for a week and then moving onto it's competitor, unfortunately I don't have the time to do this anymore for personal projects so I boiled down my choice to using a SASS options or self-hosting again, the choice was clear however I did not want to the overhead of maintaining the server I have enough of those as it is, then I needed something that met my criteria;

- Free or low cost single person license
- Built in CI/CD for simple jobs
- Support for Jenkins (because I need to learn more about Jenkins for work)
- Package/docker container storage

This ultimately narrowed down my options to Gitlab sass or github.

### Gitlab vs Github

Both offer a similar feature set but the reason I have always picked gitlab in the past is for it's additional devops features, gitlab has the ability to store terraform state's and has built gitops for kubernetes, however as this post shows I have chosen to give up those feature for github why? *money* gitlab SASS doesnt offer a single person license and the free tier just doesnt cut it for me, even including the cost of s3 storage for terraform state storage I'm increasing my costs by pennies and considering I'd have to pay for 5 licenses minimum that's a significant saving.

### TL'DR

I was tierd of maintaining the server I was hosting gitlab on, gitlab sass is too expensive for a single person to use github has the features I use the most.
