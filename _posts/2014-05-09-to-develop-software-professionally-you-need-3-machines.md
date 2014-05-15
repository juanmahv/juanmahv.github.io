---
layout: post
title: "To develop software professionally you need 3 machines"
modified: 2014-05-09 22:39:16 +0200
tags: [software,methodology]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: 
---
Quality software deserves more attention than typing some lines of code in our favorite programming language: Like many other engineering disciplines, it requires a process. I’m not talking  here about safety critical software, quality norms, etc.  It just about doing a professional, reliable, good work. 

No matter which methodology you are using: Agile or waterfall, how you organizes the teams: Centralized or distributed. No matter which paradigm you follow, TDD , MDD etc. There are 3 points you must be able to guarantee not only during the development but during the whole life of the software:

* Stability: The software must become more and more stable with the time.
* Predictability: The functionality included in each release must be clear.
* Restorability: You must be able to reproduce in the lab any release you have out in the field. 

Those requests seem reasonable, but surprisingly they get often  overlooked during the earlies development phases, that is, when you have to build up the infrastructure. In my opinion, if you want to achieve a minimum of quality you need three machines. Or better to say, three kind of machines:

Development machine
----------------------------------

Without development there is no software. I didn’t discover the world here. So the first you need is a development environment with all tools (compiler, debugger, and a SCM). Even better with some Unit Testing framework. Development and testing go hand in hand.

Deployment machine
--------------------------------

Every professional team that deserve such an adjective must automatize the deployment. I am talking about deployment in a wide sense. No just make a build, but also smoke testing and creating installation packages if needed. 

Wait a minute. You could say: Why do I need a dedicated machine for that when I can use my development one? Well, the answer is *predictability*. Creating the deployment in a controlled environment, that is,  a machine whose setup will not change during the whole project, it a good thing. So you can be sure every binary you create in you build machine are identical and therefore predictable. Trust me and you’ll sleep better at night.

There is another reason: I’m a big CI *aka Continuous Integration* fan. You can automatize the whole deployment process with tools like jenkins and you’ll know in a few minutes if the last commit broke up some test. It’s better to get such a quick feedback that getting notice just before a release when usually the only developer that can fix the bug is on vacation.

Test machine
--------------------
Yes, test are good thing. I’m not just talking here about unit testing. You can unit testing as part of the development. I’m talking about system testing, that is, making sure the binaries do run and do it as expected. This is the trickiest part and usually third party test tools are needed.

Important to keep in mind is that you need to reproduce *exactly* the same conditions under which the software is suppose to run out there. It can not be a development machine with thousand of modules and libraries already installed. Nor can be the deployment machine, for the same reason. You need such a naked environment with only the minimal required software.

Who was in the embarrassing situation where the latest release, fondly baked in your development PC refuses to install anywhere else just because libraries X,Y,Z were missing? 

I can see some faces blushing right now...
