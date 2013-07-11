---
title: Widget-ize All The Things!
layout: post
hidden: true
---

**Reduce, reuse, refactor.** That's what we do as developers _all day_.
If you're a front-end JS dev and that's not what you spend 80% of your time
doing, you're not doing it right.

The **reduce** and **refactor** are obvious necessities, and admittedly some of the most fun and/or
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

The black box approach is one that _every_ web developer is familiar with.
What I'm referring to is a UI-based plugin that might solve a problem, but requires specific
markup structure,
or an entire 300 lines of code to accomplish something you could have done in 20. It's a plugin you grab and throw
in your app/site and it's supposed to just "work". And it does for the most part. The convenience is tempting.

The problem comes when you want to customize/extend the thing to do a
little something extra. Most plugin authors don't leave room for
interaction with the plugin itself. Sure, you got a slideshow widget by
downloading a couple of files, but what good is it if it's not
fully-customizable?

The limited nature of these black box components outweighs their ease of
use.

##### Framework-based

I love MVC frameworks. On the back-end, on the front-end &mdash; if
there's a model, view, and controller, I'm doing a happy dance. When it
comes to writing reusable code, however, there's one thing I can get
annoyed with pretty easily when dealing with UI widgets in a large-scale
applications. If you do it the "framework" way, it's less reusable by
definition.

Sure you can reuse a Backbone view in any Backbone project.
But what if you switch frameworks? What if you're implementing something
on a smaller scale and don't have a framework at all? Prepare for a
couple hours of refactoring and you slowly elbow out Backbone and try to
write a more "universal" widget, all along, asking yourself, "Why on
earth didn't I just do it this way from the start?"

##### The Holy Grail

That's where option #3 comes in. The compromise between isolated,
they-just-work widgets and event-driven MVC-friendly widgets.

We're going to find a way to write UI widgets in a way that they can
work well in any context.

##### What does it look like?

A solid reusable widget should have the following criteria:

1. Options (and defaults)
2. DOM event bindings
3. Widget Logic
4. External events

You write widgets with all 4 of those and here's what you'll get.

You'll have widgets that are customizable with options, that do their
job as their supposed to and that provide ways for other pieces of an
application to hook into and modify the widget's behavior.

External events are key to allowing extensibility when writing UI
widgets.

##### Example

Let's say we have a simple widget like Bootstrap's dropdown. Before we
dive into writing such a widget, we try to identify any higher-level UI
patterns it uses (part of the reduce step I mentioned above).

The most basic aspect of the dropdown's behavior is binding the click of button
to displaying some panel. So we write a widget that does just that.
Click a button, and we'll open an associated panel. Click anywhere else
once it's opened, and we'll hide the panel for you. We'll call it a
"toggle" widget.

Now, we want an actual dropdown. That is, we want to use our little
toggle widget but change the contents of the panel to reflect what ever
items are supposed to be in the dropdown.

Take a look at the following diagram:

![](http://awes0.me/widgetize.png)

Notice that the toggle widget does it's normal thing. It's really good
at keeping track of whether or not the panel is showing, but it also
fires events when a hide or show occurs. This let's write
another bit of code that listens for the show event and tweaks the
markup of the panel to what we need.

Slick huh? Now you've got a widget that can handle a simple UI pattern,
and it's customizable/extensible enough to do something _really_ useful.

Not to mention the more-complex dropdown widget should follow the same
criteria, and provide a variety of options, as well as events for when
things are hidden/shown and whatever else the markup within the panel
should handle.

##### Wrap up

In my experience amassing an arsenal of these small pattern-based
reusable bits of code can really make working on any application so much
easier. I've tried to lay out what I think is a template for simple, but
powerful widgets so that you can go forth and widgetize all of your UI.
As always feel free to submit any comments/questions on
[Twitter](http://twitter.com/tybenz).
