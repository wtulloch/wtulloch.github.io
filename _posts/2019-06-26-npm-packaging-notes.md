---
layout: post
title:  Notes on creating NPM packages"
date:   2019-06-26 21:22:52 +1100
comments: true
categories: NPM
---
Call me stupid but I have really struggled with creating NPM packages. Not so much with the actual publishing (I mean how hard is ```npm publish```) but more around how to structure the project, what and how to include/exclude files and folders, et.al. 

I think in part it is because the idea of creating a package on occurs to me when I am part way through something I have been hacking on which usually means that project already has too many moving parts and is in no fit state for packaging. Nevertheless I start trawling through the node modules I have installed, reading OSS projects trying to grok what they have done all to just end up in a ball of frustration.  

Of course being overly ambitious in what I want to achieve doesn't help (currently I am trying to write a create- package for ```npm init```) but also, to my way of seeing if you dig into existing packages, there is no single way of structuring them or determining what files to include (do you use, ```dist``` or ```lib``` folders, should you include ```src``` and ```test```, etc).

Now the clouds have cleared. The other night I found this article by Bruno Krebs on the auth0 website: [Developing and Publishing an NPM Package](https://auth0.com/blog/developing-npm-packages). To my mind, as walk through tutorial, this article is the clearest guidance I have come across in regard to setting up and creating NPM packages and worth working through.

Next steps for me. Adapting for working with TypeScript and exploring different CI options (the tutorial uses Travis CI but CircleCI seems to be gaining traction. Also Azure Devops is to be considered).
