---
layout: post
title:  "resonance-0.0.2: It's Alive! It's ALIVE!!"
categories: projectlog resonance
---

It was a dark and stormy night. *(Well maybe only a little drizzly.)*
Goblins and ghouls walked the streets. *(Technically, they were just trick-or-treaters.)* 
Lightning struck down from the heavens. *(More like a Github push actually.)*
Then, a skeleton started to rise...

Checked into the resonance repo is a [walking skeleton]({% post_url 2013-10-23-resonance_0_0_1_a_skeleton %}). 

Now, with a single push to [Github][repo], the code is then checked out by [TravisCI][travis], tests are run against all parts of the [sbt multi-project][sbtmulti] (including [Javascript testing][sbtjstesting] via [jasmine][jasmine]), and if the tests pass, two different [microservices]({% post_url 2013-10-23-resonance_0_0_1_a_skeleton %}) get deployed to [Heroku][heroku].

There were some tricks (and treats) that I discovered along the way to creating a walking skeleton, and it would be remiss of me to not pass along what I have learned. As Mary Shelley in Frankenstein put it:

>“...learn from my miseries, and do not seek to increase your own.” 

In future posts, I will detail some of the intricacies of this skeleton, so that others can learn from *my* miseries. 

But for those wondering about resonance-0.0.3... next on my list is some skeleton radar...

[repo]: https://github.com/jasonklarsen/resonance
[travis]: https://travis-ci.org/jasonklarsen/resonance
[sbtmulti]: http://www.scala-sbt.org/0.13.0/docs/Getting-Started/Multi-Project.html
[jasmine]: http://pivotal.github.io/jasmine/
[sbtjstesting]: https://github.com/guardian/sbt-jasmine-plugin
[heroku]: http://www.heroku.com
