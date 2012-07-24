---
layout: post
title: UTM
date: 2:08PM July 24, 2012
hide: true
---

Working on ThemeRoller was a lot of fun and it taught me a lot about client-side styling and user interfaces in general.

I began the project by looking in detail at what jQuery UI ThemeRoller had done,
and trying to port it over to the client side.

Somewhere along the line, someone asked me, "Why is this tool specific to one framework? Couldn't you create a tool that
would work with any CSS file?". My immediate reaction was that this person clearly didn't understand the complexity of the problem (turns out it's not that complex at all).

But the thing is, even after wrapping up my involvement with jQuery Mobile, that person's question was still echoing in
the back of my mind. Would it be that hard to develop some framework-agnostic, WYSIWYG theme creation tool?

My immediate thought was to improve upon current developer tools. We all know Firebug and Chrome's developer tools work 
just fine for developer purposes, but I think the app I have in mind would do more for non-developers beginning to dabble
in some esoteric frameworks like jQuery Mobile or Twitter Bootstrap.

I could potentially put a little time and effort into forking/extending a tool like Firebug and adding some nice color
pickers and number spinners, but at the end of the day it wouldn't reach as many people. 

So here's my thought: create an extensible and configurable theming application that any developer can grab, tweak, and apply
for his specific framework. This way, each framework could potentially have it's own customized version of the tool, but at
the end of the day the ingredients are all the same.

What are those ingredients? Glad you asked:

1. Fast, light-weight parsing of any CSS file
2. Draggable colors with customizable behaviors
3. Undo/Redo Logs of every action the user makes
4. Downloadable/Shareable themes

Ok, so there's the overall outline. I'd love to get some feedback on the idea, as well as any questions/suggestions you
might have about implementing this thing.