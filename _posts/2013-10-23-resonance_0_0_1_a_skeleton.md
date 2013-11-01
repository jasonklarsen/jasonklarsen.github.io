---
layout: post
title:  "resonance-0.0.1: A Skeleton"
categories: projectlog resonance
---

As it is nearing Halloween, a discussion of skeletons seems in order. To quote a couple of less eerie [definitions from Merriam-Webster][skeletonDefinition]:

    skel·e·ton  \ˈske-lə-tən\
    ...
    2:  something reduced to its minimum form or essential parts
    ...
    4a :  something forming a structural framework

As per these definitions, I have checked in a skeleton as the [resonance][resonance] 0.0.1 version. Now, I don't want to confuse this with [Freeman][freeman] and [Pryce's][pryce] definition of a "Walking Skeleton" from [GOOS][goos]:

>A "walking skeleton" is an implementation of the thinnest possible slice of real functionality that we can automatically build, deploy, and test end-to-end.

A fully "walking" skeleton is my first milestone, but I felt that it was time to commit something and describe the shape that my architecture is initially taking.

My plan is to adopt the "micro-services" style for this system. I am interested in further exploring [service-oriented architectures][soa] and [micro-services][microservices] with this project. My past use of SOA has been enough to convince me that it is a lot easier to reason about smaller, independent services than a single monolithic app. (I hope to have more to say on micro-services as I explore more.)

This architecture means multiple deployed artifacts. My current plan is to use Heroku to ultimately host these services, but that is fodder for another blog post. For now, getting a multiple artifact deployment to work locally means that I have had to solve at least one [interesting problem]({% post_url 2013-10-23-microservices_and_heroku_cors %}).

From a component perspective, I have separated my design into three separate components for this first skeleton:

- `server/core` - Core domain, as in the [Ports and Adapters pattern][hexagonal]. First version is a simple stub that returns the time.
- `server/<tech>` - This adapter is in the form of an HTTP server that serves REST requests and responses. The first implementation is using [Finagle][finagle], but that could be swapped out in the future.
- `client/<type>` - Technically, a separate domain that talks to the server component over REST. The first client is a webpage version. (I do have a future itch for a mobile version, maybe iOS...) This web client could have be implemented in standard webapp style (i.e. serving the web artifacts from the same REST server), but to keep up the rigor of micro-services, I have kept them separate. 

With this architecture, comes a set of tools:

* [Scala][scala] is my language choice. I have had years of working with it, and there is a growing ecosystem of tools around it. So, I should be able to get results quickly. 
* For a build, I decided on one multi-project [sbt][sbt] project. For now, I assume that a single repository with a multi-project will be simpler. I figure that as I am adding features through the whole system, all of the projects will be changing together, and it will be easier to keep an eye on a single git repo.
* For the first version, using a framework makes a lot of sense. With Scala, you are spoiled for choice, as you can easily pull from both Java and Scala ecosystems. In this case, I decided on Twitter's [Finagle][finagle]. Twitter has embraced Scala wholeheartedly, and put their efforts into a building tools for service-oriented systems. Finagle also has some interesting companion tools for [monitoring][zipkin] and [metrics][ostrich] as well.
* On the web side, I chose [Angular][angular]. I have some experience with both Angular and Backbone.js. I ended up choosing the tool with a bit more opinion to it, as I wanted to limit my technical choices for now. As with all of these choices, I hope that this micro-services style will keep things easy to re-implement if I choose a different underlying technology later.

That's the 0.0.1 version. Next up on getting this skeleton to walk, is testing...


[skeletonDefinition]: http://www.merriam-webster.com/dictionary/skeleton
[resonance]: https://github.com/jasonklarsen/resonance
[goos]: http://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627/?tag=larsenio-20
[freeman]: http://www.higherorderlogic.com/
[pryce]: http://www.natpryce.com/
[scala]: http://www.scala-lang.org/
[sbt]: http://www.scala-sbt.org/
[soa]: http://en.wikipedia.org/wiki/Service-oriented_architecture
[microservices]: http://yobriefca.se/blog/2013/04/29/micro-service-architecture/
[hexagonal]: http://alistair.cockburn.us/Hexagonal+architecture
[finagle]: http://twitter.github.io/finagle/
[ostrich]: https://github.com/twitter/ostrich
[zipkin]: http://twitter.github.io/zipkin/
[angular]: http://angularjs.org/
