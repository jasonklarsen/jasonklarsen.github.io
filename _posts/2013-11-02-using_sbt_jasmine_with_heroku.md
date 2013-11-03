---
layout: post
title:  "On Using a SBT Plugins with Heroku"
categories: blog resonance
---

One quick pro-tip for those who are using a SBT plugin as a [project dependency][projdep] on [Heroku][heroku] and run into a problem like this:

```text
Initialized empty Git repository in /tmp/build_fa31e5a8-c0d8-4f29-b45a-c1ea4dbf3e0f/.sbt_home/.sbt/0.13/staging/e8a2b2d1a8bd62bf8e10/sbt-jasmine-plugin/.git/
fatal: Not a git repository: '.'
[error] Nonzero exit code (128): git checkout -q 0.7
[error] Use 'last' for the full log.
Project loading failed: (r)etry, (q)uit, (l)ast, or (i)gnore?  !     Failed to build app with sbt
! Push rejected, failed to compile Scala app
```

You might have noticed that buried in the first line of the output is a reference to the fact that I am using the [sbt-jasmine-plugin][sbtjas]. The documentation suggests using the plugin via a project dependency like:

```scala
lazy val plugins = Project("plugins", file("."))
  .dependsOn(uri("git://github.com/guardian/sbt-jasmine-plugin.git#1.0"))
```

Locally, SBT resolves the project fine, but using the git-based workflow of Heroku (which is pretty sweet in general), this doesn't seem to work. Rather than rock the boat and try something extra-clever (like hooking fancy [``before_deploy`` scripting][scripting] into my Travis CI build), I sought out a solution that better fit into the Heroku workflow.

I found [this tumblr post][post] that described a similar situation with a library dependency. (That post goes through a lot more detail than I do here, and do read it for a more step-by-step walkthrough of the problem.) 

The summary is to use [git submodules][submod] and let Heroku resolve the external git repo for you. In the case of a plugin, you are adding a submodule in the ``project/lib`` directory. At that point, the line in the [``project/project/Plugins.scala`` file][pluginfile], looks like:

```scala
lazy val plugins = Project("root", file("."))
    .dependsOn(file("lib/sbt-jasmine-plugin"))
```

Push it all to Heroku, and then you should be all set!

[projdep]: http://www.scala-sbt.org/release/docs/Extending/Plugins.html#d-project-dependency
[heroku]: https://www.heroku.com/
[sbtjas]: https://github.com/guardian/sbt-jasmine-plugin
[scripting]: http://about.travis-ci.org/docs/user/deployment/heroku/#Running-commands-before-and-after-deploy
[post]: http://asoftsea.tumblr.com/post/14157451282/i-think-we-can-manage
[submod]: https://devcenter.heroku.com/articles/git-submodules
[pluginfile]: https://github.com/jasonklarsen/resonance/blob/0.0.2/project/project/Plugins.scala
