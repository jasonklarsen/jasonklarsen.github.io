---
layout: post
title:  "When sbt Multi-projects and Heroku Collide"
categories: blog resonance
---

*For those wondering how the tools of sbt and Heroku can combine in new and wonderful ways, please read on. This is how I succeeded in deploying many sub-projects as separate applications via one Github repo and one SBT multi-project.*

*For the tl;dr, go to the end for the bullet point list.*

In the Scala world, [sbt][sbt] is a very popular build tool, and I am making use of its [multi-project feature][multi]. Multi-projects enable a great workflow for me. I am growing a set of small services by [vertical slices][vert], which creates small changes across each service with each slice. Working with many individual projects (each with their own git repo) would have been more overhead than I wanted for [this small project][repo].

Now, with the [0.0.2 release]({% post_url 2013-10-31-resonance_0_0_2_its_alive %}), one major part of the goal was to deploy the code to an external platform-as-a-service. For the PAAS, I chose [Heroku][heroku] as they have a very compelling (free) offering for those who are just dipping their toes into the water of PAAS like me. Heroku also has a very slick workflow -- just push to a remote git repo and you are done, right?

*Of course, things are never quite that simple.*

## Subdirectories

The first hurdle was that I ran into was deploying services from subdirectories. Heroku's normal operation assumes that you are deploying one application from the root directory of your git repository. Unfortunately, sbt multi-projects create artifacts in subdirectories, so I cannot simply deploy from the root directory. 

This problem can be resolved by revising your git workflow, and not pushing to Heroku from your root directory. One good example of this workflow is using the [``git subtree``][subtree] extension to git to separate the subdirectories. (Credit goes to [this StackOverflow post][sopost] for my first introduction to the idea.)

I think that the ``git subtree`` workflow mentioned in the StackOverflow post would work for most builds across most platforms, especially ones that have minimal or no Heroku-side compilation involved. I have a simple project, so pushing a subdirectory using ``git subtree`` should just work, right?

*Of course, things are never quite that simple.*

## sbt Build Files

With a sbt multi-project, you have the choice of using one ``build.sbt`` per sub-project, or a combined ``build.sbt`` across the whole multi-project (or some mix of the two.) For simple projects like mine, the first option, sub-project build files, would work well, as we can easily hold all of the details to build that project in each sub-directory. The multi-project in this case would just be a common parent directory that held together all of the sub-projects. If we were going to use pure sub-project build files, the ``git subtree`` workflow mentioned above would have likely worked.

What I didn't realize until later was that in order to get a Scala project to deploy on Heroku, you need to install a build plugin to package up the project. (I ended up using the [native plugin][native], not the [xsbt][xsbtone]/[sbt plugin][sbtone] mentioned on the [Heroku docs][herokudocs].) Now, things would have been fine if I could just install the build plugin in each sub-project, but sbt doesn't allow you to use the subproject's build definition to manage the installation of build plugins. (You can read what little the sbt docs say in the last line of [this section][section].)

Now, from an sbt perspective, they still give you a way to use build plugins in multi-projects. You just need to install the plugin into the root project, and call any tasks with the sub-project's prefix to invoke the functions, e.g. ``sbt client_web/stage`` instead of simply ``sbt stage``. Sweet. Now, I have sbt locally building artifacts that are ready for Heroku's consumption. So, I just tell Heroku to use my SBT subprojects, right?

*Of course, things are never quite that simple.*

## Buildpacks

Heroku does a lot of excellent work to make it easier for developers to deploy their projects across many different platforms. In fact, in the case of sbt, Heroku can auto-detect that a given project is an sbt project, and then provide a set of very sensible defaults to build and deploy that project. Those sensible defaults are stored in what is called a [buildpack][buildpack], and due to some clever file detection, Heroku is smart enough to auto-select the [Scala buildpack][scalabuildpack] for this project.

As I mentioned above, the default Heroku assumption is that you have one application per git repository. This assumption applies to the Scala buildpack as well. The sbt tasks that are run in the default Scala buildpack are ``clean compile stage``, without the flexibility to specify the subproject alias that I need.

Heroku also has the ability to specify a [custom buildpack][custom] using a command flag like ``--buildpack`` that will point Heroku to a git repository containing the buildpack to use, instead of using their built-in default buildpacks. In fact, the readme on the Scala buildpack suggests that people should use the customization ability and "hack" their buildpack to support removing Ivy caches and the like. So I can now use the ability to [clone][clone] and customize the buildpack, so that I can add my subproject prefixes to the clean/compile/stage commands. Excellent! 

So, where are we so far? We have a hacked Scala buildpack calling sub-project specific commands, using the root of the Github repository, which is also a SBT multi-project. Now, this will all just work for all sub-projects so all of the services will just deploy, right?

*Of course, things are never quite that simple.*

## Multiple Apps

Remember when I said that Heroku assumes that there is one app per git repo? Well, that seems to be a general truth architecturally, but Heroku does describe a [use case][usecase] for a single app to have multiple environments, like both a staging and a production environment. One explanation of this workflow is described in [this StackOverflow answer][stackanswer]. The simple summary of that Stack Overflow answer is that you create a separate ``git remote`` for each application, and you push to each app-specific remote to deploy to that application. So, for our multiple app case, we can create a separate ``git remote`` for each service in our multi-project.

To start to bring it all together, there is also a way to use a ``--app`` designator that you can use to run Heroku commands in your project that are specific to your given app. This combines with the ``--buildpack`` option above to specify a buildpack per application... Are we done yet?

*Of course!*

## Bringing it all together

Now, we have all of the pieces that we can put together to make this work:

1. Devise your sbt multi-project system with multiple deployable applications/services in a Github repository.
1. Create a separate Heroku app for each service through the admin facilities.
1. Create hacked buildpacks for each application that refer to the subproject tasks for each project. (I use separate named branches for each.)
1. Add different git remotes locally for each Heroku application in your Github project.
1. Configure locally the buildpack for each application in that project using the Heroku tools.
1. Deploy to each service separately by specifying different remotes in your git push.
1. Enjoy your newly deployed systems!

At this point, you can create a script to deploy to all of your systems whenever you want, or in my case, I use [continuous integration with Travis CI]({% post_url 2013-10-31-travis_ci_rocks %}) and have setup the build to automatically deploy on a successful test run. That continuous deployment is outside the scope of this post, and I will leave it to you to decide where to take Heroku and sbt next!

[stackanswer]: http://stackoverflow.com/a/5129733/12562
[usecase]: https://devcenter.heroku.com/articles/multiple-environments
[clone]: https://github.com/jasonklarsen/heroku-buildpack-scala
[custom]: https://devcenter.heroku.com/articles/buildpacks#using-a-custom-buildpack
[soPost]: http://stackoverflow.com/questions/5977234/how-can-i-push-a-part-of-my-git-repo-to-heroku
[subtree]: https://github.com/apenwarr/git-subtree
[herokudocs]: https://devcenter.heroku.com/articles/getting-started-with-scala
[native]: https://github.com/sbt/sbt-native-packager
[sbtone]: https://github.com/sbt/sbt-start-script
[xsbtone]: https://github.com/typesafehub/xsbt-start-script-plugin
[section]: http://www.scala-sbt.org/0.13.0/docs/Getting-Started/Multi-Project.html#default-root-project
[buildpack]: https://devcenter.heroku.com/articles/buildpacks
[scalabuildpack]: https://github.com/heroku/heroku-buildpack-scala
[sbt]: http://www.scala-sbt.org/
[multi]: http://www.scala-sbt.org/release/docs/Getting-Started/Multi-Project.html
[vert]: http://c2.com/cgi/wiki?SliceSystemsVertically
[heroku]: https://www.heroku.com/
[repo]: https://github.com/jasonklarsen/resonance
