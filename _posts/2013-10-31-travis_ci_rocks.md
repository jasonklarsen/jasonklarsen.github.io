---
layout: post
title:  "Travis CI Rocks!"
categories: blog resonance
---

In [creating the walking skeleton]({% post_url 2013-10-31-resonance_0_0_2_its_alive %}) for resonance, I decided to use [Travis CI][travis] for continuous building and testing of the project, as well as automated deployment to Heroku.

Travis CI has been *by far* the easiest tool to integrate into this project. They really seemed to strive to keep things simple, well-documented, and easy-to-use. I only have warm fuzzies to send their way.

In the end, my `.travis.yml` file is reasonably [complex][yml] by Travis-standards (16 lines). I have one sbt project that deploys to two different production apps using a multi-buildpack trick, but despite my complexities, I cannot complain at all about setting up the configuration. Travis CI is a great tool and a real asset to the open source community.

[travis]: https://travis-ci.org/
[yml]: https://github.com/jasonklarsen/resonance/blob/master/.travis.yml