---
title: Widget-ize All The Things!
layout: post
nav: blog
hidden: true
---

**Reduce, reuse, refactor.** That&#8217;s what we do as developers _all day_.
The **reduce** and **refactor** are obvious necessities, and admittedly some of the most fun and/or
most painful parts of the job. In my experience, the **reuse** goal is often the easiest to 
acheive. Yet, somehow, it is often the one most ignored, in the name of
"iterative development". Trust me, nothing helps you iterate more
quickly than
writing things in a generic and reusable way the _first_ time.

In my experience writing code to be purposefully reusable is fun,
challenging, and incredibly useful. I want to outline what writing
solid, reusable front-end web components should look like.

There are 3 different approaches when it comes to solving a UI problem or implementing a common UI pattern.

1. Black box
2. Framework-based
3. The sweet spot

##### Black box

The black box approach is one that _every_ web developer is familiar with.
What I&#8217;m referring to is a UI-based plugin that might solve a problem, but requires specific
markup structure,
or an entire 300 lines of code to accomplish something you could have done in 20. It&#8217;s a plugin you grab and throw
in your app/site and it&#8217;s supposed to just "work". And it does for the most part. The convenience is tempting.

The problem comes when you want to customize/extend the thing to do a
little something extra. Most plugin authors don&#8217;t leave room for
interaction with the plugin itself. Sure, you got a slideshow widget by
downloading a couple of files, but what good is it if it&#8217;s not
fully customizable?

##### Enter the example

The example I&#8217;ll be using throughout this post is what I call a toggle
widget. It&#8217;s like a dropdown but even simpler.
Click a button, a panel is shown. Click it again and it goes away.
Let&#8217;s take a look at the blackbox approach to this widget:

{% highlight javascript %}
$.fn.toggle = function( options ) {

    return this.each( function() {
        // caching objects
        var $this = $( this ),
            // limited options
            settings = $.extend({
                effect: undefined
            }, options ),
            // widget expectations
            id = $this.attr( 'href' ),
            panel = $( id );

        // widget logic
        $this.on( 'click', function( e ) {
            e.preventDefault();
            if ( panel.css( 'display' ) == 'none' ) {
                panel.show( settings.effect );
            } else {
                panel.hide( settings.effect );
            }
        });
    });

};

$( '.trigger' ).toggle();
{% endhighlight %}

This is an example of a typical jQuery plugin\*. It has few options,
expectations on markup structure, and very specific widget logic. Not
exactly ideal for integrating into a larger-scale application.

##### Framework-based

Next up is the framework-based approach to writing components. I&#8217;m
personally a big fan of MVC in app development. But, when it comes to
writing UI components, there&#8217;s one thing I get annoyed with
often &mdash; if you do it the "framework" way, it&#8217;s less reusable by
definition.

Here&#8217;s an example:

{% highlight javascript %}
var ToggleView = Backbone.View.extend({
    initialize: function(options) {
        var id = this.$el.attr( 'href' );
        this.$panel = $( id );
    },

    events: {
        'click': 'togglePanel'
    },
    
    togglePanel: function( evt ) {
        evt.preventDefault();

        if ( this.$panel.css( 'display' ) == 'none' ) {
            this.$panel.show();
        } else {
            this.$panel.hide();
        }
    }
});

var toggleView = new ToggleView({
    el: $( '.trigger' )[0],
    panel: $( '.panel' )
});
{% endhighlight %}

Notice that there are no options at all in this view. This is because
it&#8217;s custom-made for this application and we (ideally) know what our requirements are
while growing the codebase.

There&#8217;s an even bigger problem here, we&#8217;ve just rewritten a common UI
widget in a very specific "Backbone-y" way. All reusability is gone
(outside of another Backbone project that is). Converting
this code into a more flexible widget will take time and effort.

##### The Sweet Spot

That&#8217;s where option #3 comes in. The compromise between isolated,
they-just-work plugins and event-driven MVC-friendly components.

We&#8217;re going to find a way to write UI components in a way that they can
work well in any context.

##### What does it look like?

A solid reusable widget should have the following criteria:

1. Options (and defaults)
2. DOM event bindings
3. Widget Logic
4. External events

Let&#8217;s take a look at some code:

{% highlight javascript %}
var Toggle = function( el, options ) {
    var widget = this;

    // override defaults with options passed in
    options = $.extend( this.defaultOptions, options );

    this.$el = $( el );
    // selector for our trigger is an option
    this.$trigger = $( options.triggerSelector );
    this.options = options;

    // eventName is an option and the event itself
    // calls a method that can be overridden
    this.$trigger.on( options.eventName, function( evt ) {
        widget._handleEvent( evt );
    });
};

$.extend( Toggle.prototype, {
    defaultOptions: {
        triggerSelector: '.trigger',
        eventName: 'click',
        preventDefault: true,
        showClassName: 'show',
        hideClassName: 'hide'
    },

    _handleEvent: function( evt ) {
        var opts = this.options,
            $el = this.$el,
            $target = $( evt.target );

        if ( opts.preventDefault ) {
            evt.preventDefault();
        }

        this.trigger( 'before-toggle', { panel: $el, trigger: $target } );

        if ( $el.hasClass( opts.showClassName ) ) {
            this.trigger( 'before-toggle-hide', { panel: $el, trigger: $target } );
            this.hide();
            // Events triggered...
            this.trigger( 'toggle-hide', { panel: $el, trigger: $target } );
        } else {
            this.trigger( 'before-toggle-show', { panel: $el, trigger: $target } );
            this.show();
            // ... when important things happen
            this.trigger( 'toggle-show', { panel: $el, trigger: $target } );
        }

        // Save last clicked trigger
        this.lastClicked = evt.target;
    },

    // No longer hide and show but rather just applying/removing
    // classes
    hide: function() {
        this.$el.removeClass( this.options.showClassName ).addClass( this.options.hideClassName );
    },

    show: function() {
        this.$el.removeClass( this.options.hideClassName ).addClass( this.options.showClassName );
    },

    trigger: function( evt, data ) {
        $( this ).trigger( evt, data );
    },

    on: function( evt, fn ) {
        $( this ).on( evt, fn );
    }
});

$.fn.toggle = function( options ) {
    return this.each( function() {
        var widget = new Toggle( this, options );
    });
};

var toggle = new Toggle( '.panel' ); // or $( '.panel' ).toggle();
{% endhighlight %}

Here have a component that is customizable with options, that does its
job as it&#8217;s supposed to and that provides ways for other pieces of an
application to hook into and modify its behavior.

Admittedly, this implementation is much longer, but that&#8217;s the price of
writing something that will work anywhere

##### Dropdown

Let&#8217;s take our example even further. Now, we want an actual dropdown.
That is, we want to use our little
toggle widget but change the contents of the panel to reflect what ever
items are supposed to be in the dropdown. And we want our widget to be
smart enough that multiple toggles can control the same dropdown and have
different markup depending on which toggle was clicked.

{% highlight javascript %}
var Dropdown = function( ele, templateFn, options ) {
    var widget = this,
        toggle = new Toggle( ele );

    // Need options for the dropdown widget itself
    options = $.extend( {}, this.defaultOptions, options );

    this.options = options;

    toggle.on( 'before-toggle', function( evt, data ) {
        // The toggle widget saves the last trigger used
        // If the trigger that was clicked is different than this one
        // we hide the panel to override the default toggle behavior
        if ( data.trigger[0] != toggle.lastClicked ) {
            toggle.hide();
        }

        var panel = data.panel,
            trigger = data.trigger,
            templateData = trigger.data( options.dataAttr );

        // Please for the love of all things good, use a templating engine
        panel.html( templateFn( templateData ) ).addClass( options.dropdownClassName );

        // Small (resuable) utility to position elements relative to others
        var pos = positionElementAroundAnother( trigger, panel, {
            align: 'right',
            alignOffset: 10,
            position: 'below',
            positionOffset: 40
        });

        panel.css({
            'top': pos.y + 'px',
            'left': pos.x + 'px'
        });
    });
    
    toggle.on( 'toggle-show', function( evt, data ) {
        widget.trigger( 'dropdown-show', data );
    });

    toggle.on( 'toggle-hide', function( evt, data ) {
        widget.trigger( 'dropdown-hide', data );
    });
};

$.extend( Dropdown.prototype, {
    defaultOptions: {
        dropdownClassName: 'dropdown',
        dataAttr: 'dropdown-list'
    },
    
    trigger: function( evt, data ) {
        $( this ).trigger( evt, data );
    },

    on: function( evt, fn ) {
        $( this ).on( evt, fn );
    }
});

// Setting up dropdown object for each trigger
$( '.trigger' ).each( function() {
    var $this = $( this ),
        list = $.map( $this.data( 'items' ).split( ',' ), function( item ) {
            return {
                link: '#' + item.toLowerCase().replace( /\s/g, '-' ),
                text: item
            };
        });

    // Set our template object for the dropdown
    // on a data attribute
    $this.data( 'list', { list: list } );
});

var dd = new Dropdown( '.panel', function( data ) {
    return _.template( $( '#dd-menu-template' ).text(), data );
}, { dataAttr: 'list' } );
{% endhighlight %}

Live demo [here](http://codepen.io/tybenz/pen/BIvAo).

Notice that the toggle widget does it&#8217;s normal thing. It&#8217;s really good
at keeping track of whether or not the panel is showing, but it also
fires events when a hide or show occurs. This let&#8217;s write
another component "Dropdown" that listens for the before show event and tweaks the
markup of the panel to what we need.

Slick huh? Now you&#8217;ve got a widget that can handle a simple UI pattern,
and it&#8217;s customizable/extensible enough to do something _really_ useful.

Not to mention the more-complex dropdown widget follows the same
principles. It provides options, as well as events for when
things are hidden/shown.

It&#8217;s also flexible in other respects.
The markup generation for the dropdown is left outside of the widget. That
should be left up to the developer to do as they please. In this case, I&#8217;m using
data attributes and a templating engine to generate the markup.

##### Wrap up

In my experience amassing an arsenal of these small pattern-based
reusable bits of code can really make working on any application so much
easier. Give it a try and you&#8217;ll find that building out a library of
these types of components is extremely rewarding and in general, it will
make you a better developer.

I&#8217;ve tried to lay out what I think is a template for simple, but
powerful components so that you can go forth and widgetize all of your UI.
As always feel free to submit any comments/questions on
[Twitter](http://twitter.com/tybenz).
