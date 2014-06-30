---
layout: post
title: "Why is a good idea to tag your releases"
modified: 2014-06-30 23:05:24 +0200
tags: [software]
image:
  feature: 
  credit: 
  creditlink: 
comments: yes
share: 
---
 
It has become habit for me in the last years to tag all releases -or at least all that will be shipped- with a tag with the latest commit number/hash. Why? because I believe just the old traditional version number is not enough.
 
I firmly believe each binary has to be linked to the code it derives from. Given the case we need to recreate the conditions in which that release was built, at least regarding source code, we can. This need may not arise during the development and design of the software, but later on, when bugs get reported from the field, it's important to be able to travel back in time and have a look to how looked certain part of the code at build.
 
You could say, well, that's why a version number is for. Well..not quite.
 
Not every single build gets always a new version number. The format <mayor number>.<minor number>.<path number> is open to interpretation. Of course anyone can write a batch script to add the build number to the version string, but that's not the way to go.
 
Keep in mind that not all releases produced by your build machine get delivered to customers. Appending the commit information to the version may lead to confusion, as is difficult for the end user to understand why the releases jumped form 5.2.34 to 5.2.112 with nothing in between.
 
Moreover, it may happen that our commit are not identified by decimal number but by hash. This is the case if you're using git as SCM tool. Naming a release 5.2.a3456ef88h makes thing definitely not easier.
 
But first of all, release numbers should not be one to one with build numbers. We do want the flexibility of keep them apart. They're aimed to different audience. Version number is a way to tell the user how much did the software change in comparison with the previous version. We can put a policy around to tell when to increase the major or minor number but at the end is a management decision. Even the marketing people have a say in this.
 
In the other hand, build number is the kind of information only developers care. Too technical for the rest.
 
Like the version number, a good place for the build number is the resource file. Built as part of the binary. Let's keep both number together, but do not mix.
 