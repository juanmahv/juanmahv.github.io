---
layout: post
title: "Code repositories are not just for code"
modified: 2014-07-16 21:59:20 +0200
tags: [software]
image:
  feature: 
  credit: 
  creditlink: 
comments: yes
share: 
---

I still hear the sentence now and then "Do not check in. Code repository is just for code". Usually vehemently spoken and followed by self hair pulling. Well, I dare to disagree.

I believe your code repository is the perfect place for all artifacts needed to build the project. No just source code, but also:

* Documents: Requirements documents, architecture drafts, user Manual, UI sketches, test plan, etc.

* Deployment scripts: In case you use some CI server like Jenkins, its better  to store the deployment code in scripts and save them in your repository than putting the logic into the server right away. Otherwise if a build machine burns, you'll spend a lot of time recreating the setup. In the other hand, by storing the deployment logic in a file, all the new server has to do is calling a script.

* Test data: Some unit test need mocked data. The repository y the right place for that.

One of the advantages of saving all your project stuff under version control is all developers are automatically synchronized each time they pull from repository. So you so not have to worry about different teams working with different API versions, for instance. Keep in mind the repository is at the end a way of communication between all team members -and not just developers-. Make it work as such.