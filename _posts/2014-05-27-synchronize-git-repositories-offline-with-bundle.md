---
layout: post
title: "Synchronize git repositories offline with bundle"
modified: 2014-05-27 22:58:28 +0200
tags: [git]
image:
  feature: 
  credit: 
  creditlink: 
comments: yes
share: yes
---

Git is probably the best scm nowadays. The list of features is simply amazing and is hard to imagine a work-flow git is not able to serve.

One of my favorite is sharing commits, that is, synchronize, between 2 repositories not connected over a network. 

Imagine you have to deliver your latest changes to another developer and your repositories cannot see each other. Let’s say due to firewall or even the absence of a physical network. This use case may sound weird, but something similar happened recently to me, and believe me, when it comes you’re happy that git  is your scm.

There is a git command for these cases: bundle. As the name suggest, its purpose is to collect a set of commits together in a binary file for transportation. 

Let’s see an example: Imagine imagine one of your coworkers need all your changes since a certain commit f8469b7a4b. Let’s say we all are working in a branch called *develop*. So putting all commits together in a bundle file is so easy as typing:

	git bundle create ../mybundle.gitbundle f8469b7a4b..develop
 
The reason of ‘../’ is that I prefer creating the bundle file outside the repository tree to keep things more clear, but it’s up to you.

It’s a good practice to check the bundle integrity before delivering. So we just type:

	git bundle verify ../mybundle.gitbundle

If everything is OK, the file is suitable for distribution. 

In the other side, to apply the commits inside the bundle, just call

	git pull mybundle.gitbundle develop develop
 
and the changes will flow into the repository.

Easy, isn’t it? A latest tip. I recommend to set a tag to the latest commit of the bundle. Just to know where to start the next synchronization.