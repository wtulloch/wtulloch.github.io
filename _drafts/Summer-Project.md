---
layout: post
title:  "The (Southern) Summer Project"
date:   2014-02-01 21:22:52 +1100
comments: true
categories: ASP.NET Web
---
## The premise ##
Now that ASP.NET 5 is close to release I thought this would be a good time to really take it for a run.
Rather than create something from scratch I have decided to take an existing production application and see what it would take to convert it to ASP.NET 5.

The existing application was written nearly three years ago as a SPA on top of ASP.NET 4.0. The key technologies that make up the application (both server-side and client) are:
    - ASP.NET MVC
    - ASP.NET Web Api 1.0
    - AngularJS 1.2.?
    - Bootstrap 2.?
    - Entity Framework 5 (though this has been updated to 6 I think)
    - SignalR 1.?
    - RavenDB
    - SQLServer
    - Autofac
    - Various other bits and pieces

The goal of this exercise is to see what it would take to update the application (if it can be done) and for myself to get a better understanding some of the technologies being used in web development. 

## Why even bother? ##
Curiosity more than anything else but there are some other factors at play.
The first one is that is application was the last big web application I had worked on and by taking something I am Intimately familar with will allow me to focus where the differences are.

On a more general level I am interested in how manage the problem of applications ageing and becoming stuck in a technological backwater. 

Even though this application is only 2+ years old we have already hit some issues with inconsistencies between browser and framework. For example the client recently updated to IE 10 (they are government so things move slowly) which caused a problem with AngularJS and the displaying data on the page. Updating to the latest version of Angular fixed the initial problem but this introduced some unwanted side-effects. To solve we ended up testing earlier version of Angular till we found the one that fixed the initial issue without side-effects.

Given how client-side libraries in particular are evolving I can see that this is going to getting worse as time passes. So the question is; what would it take to update and existing web application?

## How is this going to play ##
My rough plan is as follows:
- Learn how to use Bower to pull in all of the client-side dependencies while maintaining the existing script folder structure.
- Incorporating Gulp and Grunt
- Convert all the existing custom javscript files to Typescript (this is just an excuse to learn Typescript).
- Update to Entity Framework 7
- Update the Restful services
- convert from Bootstrap 2 to 3
- Reimplement the custom authentication model
- Rewriting version 1 web api controllers
