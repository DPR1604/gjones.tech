---
title: "Automating Hugo"
subtitle: "How I've automated the deployment of hugo and set up my environments"
date: 2023-08-30T12:57:55Z
lastmod: 2023-08-30T12:57:55Z
draft: true
authors: []
description: ""
tags: ["self-hosting", "hugo", "automation"]
categories: ["Hugo"]
series: ["Hugo"]
resources:
- name: "featured-image"
  src: "featured-image.png"
---

In this post I'll be discussing how I have automated the deployment of hugo both to a development environment and production environment.

With hugo I wanted to achieve 2 things a new speedy blog and using my DevOps skills to deploy it, this is probably going to evolve in the future as I start venturing into hosting my own kubernetes cluster but the purpose of this blog is document the journey not the destination.

### The environment

#### Server
Both the development and production sites are hosted on the same server under different hostnames but the CI/CD pipeline is set up to be able to separate this in the future should I need to.

The server in question is my pre-existing LAMP stack I still run a number of applications that use PHP but I'll be looking at containerizing these applications to aid in updating them and the move to kubernetes I'm planning, this server is overkill for a hugo site but the server still needs the extra resource.

- CPU: 4 core
- RAM: 8GB
- webserver: nginx

#### SCM 
My SCM (Source Control Management) of choice is Gitlab EE, it's what I'm the most familiar with as it's what I use for work, the sites source is available to view [here](https://gitlab.valhallaonline.info/publicgroup/public-websites/gjones.tech) I'll be using gitlab CI/CD to deliver the site.

#### Pipeline

```yaml
---

image: registry.gitlab.com/pages/hugo/hugo_extended:latest

variables:
  DOCKER_TLS_CERTDIR: "/certs"

stages:
  - test
  - deploy

before_script:
  - apk add openssh-client rsync
  - eval $(ssh-agent -s)
  - echo "$SSH_PRIVATE_KEY" |tr -d '\r' | ssh-add -

.test:
  stage: test
  script:
    - if [ "$UPDATE_THEME" = true ]; then git submodule update --init; fi
    - hugo

.deploy-site:
  stage: deploy
  script:
    - if [ "$UPDATE_THEME" = true ]; then git submodule update --init; fi
    - hugo
    - rsync -e "ssh -o StrictHostKeyChecking=no" -crtvz --delete ./public/ $SSH_USERNAME@$HOSTNAME:$SITE_PATH

test:
  environment:
    name: Development
  rules:
    - if: $CI_COMMIT_BRANCH == "$CI_DEFAULT_BRANCH"
      when: never
    - when: always
  extends: .test
  
dev-deploy-site:
  variables:
    HUGO_BUILD_DRAFTS: "true"
  environment:
    name: Development
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: never
    - when: on_success
  extends: .deploy-site

prod-deploy-site:
  environment:
    name: Production
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: always
    - when: never
  extends: .deploy-site
```

Lets breakdown the above.

```yaml
image: registry.gitlab.com/pages/hugo/hugo_extended:latest
```
The image defines the docker container that will be used for the build thankfully Gitlab are kind enough to provide one for this purpose.

```yaml
variables:
  DOCKER_TLS_CERTDIR: "/certs"
```
These are variables used in every stage in the pipeline.

```yaml
before_script:
  - apk add openssh-client rsync
  - eval $(ssh-agent -s)
  - echo "$SSH_PRIVATE_KEY" |tr -d '\r' | ssh-add -
```
The before script does 3 things
* Installs openssh client and rsync both are used to create a secure connection to the webserver and transfer the files.
* Starts up the ssh agent
* Adds the SSH private key stored as variable inside gitlab

```yaml
.test:
  stage: test
  script:
    - if [ "$UPDATE_THEME" = true ]; then git submodule update --init; fi
    - hugo

.deploy-site:
  stage: deploy
  script:
    - if [ "$UPDATE_THEME" = true ]; then git submodule update --init; fi
    - hugo
    - rsync -e "ssh -o StrictHostKeyChecking=no" -crtvz --delete ./public/ $SSH_USERNAME@$HOSTNAME:$SITE_PATH
```
I templated out the two tasks that are used these are extended later on but I plan on moving these to my CI/CD templates repo in the future as I plan to have more sites use hugo.

```yaml
test:
  environment:
    name: Development
  rules:
    - if: $CI_COMMIT_BRANCH == "$CI_DEFAULT_BRANCH"
      when: never
    - when: always
  extends: .test
  
dev-deploy-site:
  variables:
    HUGO_BUILD_DRAFTS: "true"
  environment:
    name: Development
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: never
    - when: on_success
  extends: .deploy-site

prod-deploy-site:
  environment:
    name: Production
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
      when: always
    - when: never
  extends: .deploy-site
```

Here we have the jobs themselves, test is only run on none default branches (anything that's not the master branch), the deploy job are functionally the same the only differences are the file path and if the drafts are also built, this is all defined by which environment is used.

To tl'dr the above code is pushed to a dev branch the pipeline is run and the site files are deployed to the dev site, the site in manually tested, usually checked for formatting errors, once happy a merge request is complete and then the pipeline deploys to the prod site, with a test job the deployment is averaging between 20 and 25 seconds, this could be improved by adding a cached image but I'm happy with the speed.

In the future I'll be adding a check to to this pipeline to black merge requests if there are any posts that are still marked as drafts to catch that problem.