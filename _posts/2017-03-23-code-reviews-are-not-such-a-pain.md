---
layout: post
title: "Code reviews are not such a pain"
modified: 2017-03-23 23:05:22 +0100
tags: [software]
image:
  feature:
  credit:
  creditlink:
comments: yes
share:
---
I'm a great fan of code reviews. I admit it took a while for me to get used to, but the effort pays off.

One of the most obvious benefits of code reviews is to spot bugs. Four eyes see more than two. Specially when  one of those two wrote the code originally and is conditioned by her line of toughs. We programmers tend to not be too critical with our own code. Therefore just having a colleague looking over our shoulder and asking stupid questions (or not so stupid) can improve the code quality a lot. Of course, like any team sport, review pairing has to be trained and fair played. Do not discuss ten minutes about the proper name of a method and let that memory leak to go overlooked. Review teams ha to focus on what really matter, and really get along with each other.  Egos must be left apart.

But there is another great advantage of code reviews and quite often  underestimated: they are a marvelous vehicle of know how transfer. Reviews are the best playground for senior developers to transfer technical and business  knowledge to juniors.

Code reviews can be synchronous ( two or more people sit together physically or remotely and go through the code ), or asynchronous (pushing the changes to a repository and let people review them by their own at their convenient). Both approach have pro and cons. In synchronous reviews the communication is much better, but depending on the  working environment picking the right people at the right moment for a review is not that easy.

In the other hand, asynchronous reviews  let people better schedule their review session, but at the cost of isolation.

In my opinion a combination of both is the best. I recommend asynchronous reviews in most cases, and synchronous reviews when you have to go over critical code or you're in time pressure.
