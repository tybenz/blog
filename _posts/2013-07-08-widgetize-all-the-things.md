---
title: Widget-ize All The Things!
layout: post
hidden: true
---

*Reduce, reuse, refactor.* That's what we do as developers _all day_.
If you're a front-end JS dev and that's not what you spend 80% of your time
doing, you're not doing it right.

The reduce and refactor are obvious necessities, and admittedly some of the most fun and/or
most painful parts of the job. In my experience, the reuse goal is often the easiest to acheive.
Yet, somehow, it is often the one most ignored.

Writing code that is purposefully structured to be reusable has immeasurable value. It's not that hard,
it's a lot of fun, and it just makes life better.

I've seen recently a trend in the world of web development of this topic surfacing
more and more, and this is my two-cents. In the world of front-end development stock-piling
a treasure chest of well-written reusable widgets might as well be equivalent to hoarding pure gold.

Let me break it down for you.

There are 3 different approaches when it comes to solving a UI problem or implementing a common UI pattern.

1. Black box
2. Framework-based
3. The holy grail

##### Black box

The black box approach is one that _every_ web developer is familiar with. We have frameworks like jQuery to
thank for that. What I'm referring to is a UI-based plugin that might solve a problem, but requires specific
markup structure,
or an entire 300 lines of code to accomplish something you could have done in 20. It's a plugin you grab and throw
in your app/site and it's supposed to just "work". And it does for the most part. The convenience is tempting.

The problem comes when you try to customize/extend the things to do something a little extra.
