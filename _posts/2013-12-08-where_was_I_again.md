---
layout: post
title:  "How Jason Got His Context Back"
categories: blog
---

As I write this post, I come off a month-long hiatus where family holidays and end-of-year work obligations came and went. During this time, I didn't spend any time focused on my personal project ([resonance][res]), nor this blog, but I did have the foresight to use my favorite coding trick to bookmark exactly where I was:

*At the end of the coding session, write a broken test that tell you where to begin next time.*

In my case, I have a few git repos to choose from, and I couldn't even remember what codebase I was last working. So, I just ran tests in each project until I found the failing tests. Then as soon as I read the errors... BAM! My context returned. When it is only a couple days between sessions, I can often remember where to pick up, but in this case of weeks, I totally forgot.

I find this a less stressful form of the [Zeigarnik effect][zeigarnik]. The Zeigarnik effect is the psychological theory that incomplete tasks "stick in the mind" easier than complete tasks. (While my broken test trick is more of a memory trigger, I do use the Zeigarnik effect more directly to think about my current projects. It is good for when I have time to think, but not to type, e.g. when I am on the train or out for a walk.) 

Now, I don't want to take full credit for this broken test trick. I am pretty sure that it was suggested to me many years ago. A quick Google finds it [here][trick] and [here][trick2] as a TDD best practice, but I also saw it mentioned in passing in several other links. 

Regardless of the true source of this broken test trick, I do recommend it to others to try when you need to keep your context over multiple sessions of coding.

[zeigarnik]: http://blog.sandglaz.com/zeigarnik-effect-scientific-key-to-better-work/
[trick]: http://codelikethis.com/lessons/agile_development/test_driven#leave_one_for_tomorrow
[trick2]: https://coderwall.com/p/987g0q
[res]: http://larsen.io/resonance.html