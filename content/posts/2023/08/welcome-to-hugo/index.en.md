---
title: "Welcome to Hugo"
description: "A quick post discussing hugo and why I've chosen it"
date: 2023-08-25T14:02:57Z
lastmod: 2023-08-29T11:26:06Z
draft: false
author: "Gareth Jones"
tags: ["self-hosting", "hugo", "automation"]
categories: ["Hugo"]
resources:
- name: "featured-image"
  src: "featured-image.png"
---

Here it is the new hugo site, I was on the hunt for a new faster way to deliver my blog and I stumbled across hugo, hugo is a static site generator that's written in Go and utilizing markdown to write posts, essentially it works by writing your posts in markdown then you use hugo to generate static html files.

Because the site is just static HTML it loads incredibly fast and pairing it with cloudflare's CDN the load on the web-server is significantly reduced, which will in the near future will be replaced with a kubernetes setup.

### Why hugo?

I've chosen hugo for a number of reasons but these are the main reasons;

* Speed, not just site performance but the speed at which the site can be built is brilliant.
* Simple Automation, I wanted something I could utilize my DevOps skills on, using Hugo I've been able to write a gitlab pipeline to deploy the site to both a staging site and production a post will be coming about this soon.
* A simple language, yes I know "how is this more simple then wordpress", as an avid user of wordpress I know it's pros and cons inside and out and for my purpose this is better, I just wanted something simple and fast hugo is both of those.
* Dependencies, once the site it build it's just HTML which means I only need a web-server to serve the files my plan is to have the site built in gitlab and pushed to the webserver and eventually it will be bundled into a container.

### Coming up

This post is the start of a series on hugo and my journey using it, there will be some posts on the automation that delivers the site and how to set up hugo if you'd like to do the same.