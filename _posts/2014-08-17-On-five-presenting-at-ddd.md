---
layout: post
title:  "Ruminations on five years presenting at DDD Melbourne"
date:   2014-08-17 21:22:52 +1100
categories: Stuff
comments: true
---

A few weeks ago, along with Aaron Powell, I was recognised for being voted up five years in a row for DDD melbourne. 
Aaron has already blogged on his experience over those five years [5 Years of DDD Melbourne](http://www.aaron-powell.com/posts/2014-07-22-5-years-of-dddmelb.html)
 and not to be out done I thought I would chip in with my two cents worth.

In this post I am going to reflect back on those five years and look at some of the things I have learnt.

##2010 - Code Contracts
When the first DDD Melbourne was announced I thought this would be a bit of a hoot so tossed my hat into the ring.

I had over the years done presentations before, including a senate committee hearing once, and in a previous life I had been a musician so I had some familiarity with standing in front of a room full of people and making a fool of myself. A technical presentation in front of your peers though that is a different kettle of fish.

This was around the time that Visual Studio 2010 had just been released and out of some kind of perversity proposed a talk on Code Contracts. To be honest I didn't think this would get voted up but it did. 

For a first-timer the talk went well. I was lucky in my choice of topic; Code Contracts is/was a reasonably self-contained so I avoided the classic first-time presenters trap of running out of presentation time though it was a close run thing. 

##2011 - Securing web services (WCF) with WIF
I had been an early fan of claims-based identity and in particular working with WIF. At the time I did this talk I had been working at client where I had developed a custom STS to issue SAML tokens for securing WCF services and it was this work that I used for the basis of this talk. 

My intention was to take the audience through the entire process; from creating a custom Security Token Service (STS) through to using it to secure a WCF service. In hindsight I probably overreached a little bit on this one and tried to cover too much which meant I ended rushing towards the end.
This was a good learning experience through. Some of the keys things that I learnt were:

* **Don't be too clever** - I invested too much energy creating a back story for my presentation and the "humour" of it was lost on anyone who didn't know anything about the New York art scene in the 60's (really doesn't anyone know about Andy Warhol and The Factory). 
* **Too much code** - I ended up writing too much boiler plate code which really didn't add anything to the talk. Additionally by using code snippets the audience really didn't get a chance to understand what was happening. 

##2012 - What's new in .NET 4.5
At the time of this talk I was co-authoring a book on .NET 4.5 with two of my colleagues which formed the basis for this talk. This was what I think of as a shopping list presentation. 

For this talk I took eight of the key new features in .NET 4.5 and gave a brief rundown on each of them. Again, in hindsight, I think the code examples didn't always hit the mark. This was in part due to the nature of shopping list talks, since you are essentially jumping from topic to the next without any really story arc. This meant the demo code couldn't be built up and thus giving the audience a context for the examples which were occasionally a bit laboured.

##2013 - Securing .NET WEB API
I was personally very happy with this talk. The story arc and timing all came together. No rushing to get to the end and all the demos made sense and kept the audience engaged. 

One of the key things I did this time was to show key code concepts in my slides rather than in Visual Studio. This allowed me to remove any extraneous information and keep the focus on what was important. This also meant that when running the code demos the audience already had some context for what was being shown. 

##2014 - Don't Say F*$k to the Client and other war stories
I had been playing around with the idea of doing a non-technical talk for a while and this was the result. The intention had been for a light-hearted talk but as I worked up the stories things took a more serious tone. 

Though this was probably the hardest talk I have done, there was no code to hide behind, on a personal level I think it was one of my best and the feedback from the audience was positive.

The hardest part of developing this talk was finding a "voice" for the presentation and I spent a lot of time writing, editing and rejecting stories to discover it. 

##In Summary
I love presenting. It is challenging, requires work and I would be lying if I didn't admit to being scared shitless every time I did it but the payoff has always been worth it.

The following is ragtag collection of some of the things I have learnt over the last five years.

* **Avoid dot points.**  
Too often I had attended talks where every slide shown was crowded out with dot points. Effect of this is that your audience has to split their focus between what you are saying and reading what is on the screen. My preference is to use slides primary as context markers.

* **Words are a visual element.**  
Think of words as images as opposed to text on a page. 

* **Coding in front of an audience is hard**  
You do it everyday, how hard can it be. Bloody hard, especially when you make a mistake and half the audience are shouting out suggestions.   
 If you are going to do code examples you really need to plan ahead and keep it concise. Also practice coding standing up, very rarely do you do a talk sitting down. Finally learn to talk while you writing code. There is nothing worse then the silence of an auditorium while you bash away at a keyboard for five minutes. Keep the audience engaged, explain what you are doing.

* **Giving a talk is entertainment not a form of punishment.**  
Yes it is a technical talk but that is no reason to be boring. 

* **In the beginning get all your ideas down and then edit ruthlessly.**  
It is easy to overload a talk with unnecessary detail. When I first start a working on a talk I use a mind map to capture as much as I can and then start shaping the story I want to tell. 

* **Writing demo code takes time.**   
Give yourself enough time to get it right and always have a fall-back position if you intend to code live in case things don't go as planned.  

* **Don't read from your slides, talk to them.**  
There is nothing more tedious then a presenter who recites word for word the content of their slides. Additionally this means you end up not engaging the audience (see the next point).

* **Talk to your audience.**  
When people attend your presentation they are there to hear you talk so do them the courtesy of engaging with them.  

* **You will always be nervous before you present.**   
I always have this moment, about an hour out from when I have to present, when the weight of stupidity descends. The thing is if you have done the prep it should all fall into place.

* **Practise your talk.**   
This is not one of my strong points, let's face talking aloud to yourself can feel a bit weird. The thing is it doesn't have to be word perfect but it will help you get the talk's rhythm. For the last talk I did I practiced my talk by dictating to the computer. This allowed me to practise my delivery as well as fine tune the content.

