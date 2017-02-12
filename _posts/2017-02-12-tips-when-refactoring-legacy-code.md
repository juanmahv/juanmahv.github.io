---
layout: post
title: "Tips when refactoring legacy code"
modified: 2017-02-12 11:57:54 +0100
tags: [software development]
image:
  feature: 
  credit: 
  creditlink: 
comments: yes
share: 
---

It may happened you discover a critical bug that need to be fixed asap. Of course, Murphy’s law for software development correctly says that in those situations, the people that wrote the code already left the company or at least are in vacation. So you’re alone. 

So you managed to reproduced the error,  had a look to the code and tried to understand the whole stuff. Now it’s were the fun begins.

In the traditional way, you start debugging the code, make some changes, debugging again, and  repeat the cycle until you’re confident enough you didn’t break anything. Deliver your changes, test as deeply as a possible and if you’re lucky, the bug is gone.

But things rarely go so smoothly. Usually, after your changes, if you’re lucky, you realize you broke something at another part of the software, turn your attention into that piece of code, and debug again. Now you have more code in scope to debug. And the snow ball grows and grows... And as I said, this is the fortunate situation. If you’re unlucky, the error goes overlooked and ends up delivered to customers. Ouch!

There is a way to minimize this risk: Unit testing. Unit testing works at best when the tests are developed at the same time than the rest of code. Ideally by the same people. But in this case,  there is nothing. 

Well, the first step is to wrap the code around enough test cases that you are reasonably confident you won’t break anything. Pay attention and spend as much time as possible in constructing a solid test harness. This will pay off!

The tricky part is how to be sure we catch up everything in our tests. This is difficult when dealing with unfamiliar code, but here are some tips the helps:

* Write your unit test *before* you change any single line of the code. This is important to guarantee we won’t modify the original behavior unexpectedly.

* In a pure functional environment, we only need to check the returned value of the methods you want to refactor. But code is rarely pure functional, so we have to check the content of the variables before and after running the methods (or in a more technical way, the side effects).  You can see the final values with the debugger. However, pay attention that debug versions may differ from release ones. For instance in the initialization of variables. Make sure you check ALL variables modified to avoid holes in your test.

* Once you have the initial and final values of the variables modified by the method, write a test using the initial values as precondition and the final ones as postcondition. Run the test, as obviously it will pass. By now we didn’t change anything, just prepared the test harness. Note that we don’t say the method works correctly (in fact, we still have a bug to fix) but now we have a safe playground to work.

* Now we can fix the bug. The fix can be a small change or a complete refactoring, but now we have a way to tell the impact of our changes. 

After the fix, you have to adapt the test to the new behavior, but there changes are now under control and the impact well known.  An at the end, in addition to the fix you have as reward for your effort a unit test infrastructure that makes future maintenance much safer and easier.

If you need to modify legacy code, invest time and effort in a solid test harness around your code. Of course the tricky part is to catch up all side effects, and  here is were the skill of a good software developer makes the difference. Following this technique can save you many hours in front of the debugger and minimize risk of bugs in your final release.

