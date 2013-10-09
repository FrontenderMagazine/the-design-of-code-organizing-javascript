The Design of Code: Organizing JavaScript
------------------------------------------------------------

Great design is a product of care and attention applied to areas that matter,
resulting in a useful, understandable, and hopefully beautiful user interface.
But don’t be fooled into thinking that design is left only for designers.

There is a lot of design in code, and I don’t mean code that builds the user
interface—I mean the design*of* code.

Well-designed code is much easier to maintain, optimize, and extend, making for
more efficient developers. That means more focus and energy can be spent on
building great things, which makes everyone happy—users, developers, and
stakeholders.

There are three high-level, language-agnostic aspects to code design that are
particularly important.

1.  System architecture—The basic layout of the codebase. Rules that govern
    how various components, such as models, views, and controllers, interact witheach other.
2.  Maintainability—How well can the code be improved and extended?
3.  Reusability—How reusable are the application’s components? How easily
    can each implementation of a component be customized?


In looser languages, specifically JavaScript, it takes a bit of discipline to
write well-designed code. The JavaScript environment is so forgiving that it’s
easy to throw bits and pieces everywhere and still have things work.
Establishing system architecture early (and sticking to it!) provides
constraints to your codebase, ensuring consistency throughout.

One approach I’m fond of consists of a tried-and-true software design pattern
, the module pattern, whose extensible structure lends itself to a solid system
architecture and a maintainable codebase. I like building modules within a
jQuery plugin, which makes for beautiful reusability, provides robust options,
and exposes a well-crafted API.

Below, I’ll walk through how to craft your code into well-organized
components that can be reused in projects to come.

## The module pattern

There are a *lot* of design patterns out there, and equally as many resources
on them.[Addy Osmani][1] wrote an [amazing (free!) book][2] on design patterns
in JavaScript, which I highly recommend to developers of all levels.

The [module pattern][3] is a simple structural foundation that can help keep
your code clean and organized. A “module” is just a standard object literal
containing methods and properties, and that simplicity is the best thing about
this pattern: even someone unfamiliar with traditional software design patterns
would be able to look at the code and instantly understand how it works.

In applications that use this pattern, each component gets its own distinct
module. For example, to build autocomplete functionality, you’d create a module
for the textfield and a module for the results list. These two modules would
work together, but the textfield code wouldn’t touch the results list code, and
vice versa.

That decoupling of components is why the module pattern is great for building
solid system architecture. Relationships within the application are well-defined;
anything related to the textfield is managed by the textfield module, not strewn
throughout the codebase—resulting in clear code.

Another benefit of module-based organization is that it is inherently
maintainable. Modules can be improved and optimized independently without
affecting any other part of the application.

I used the module pattern for the basic structure of [jPanelMenu][4], the
jQuery plugin I built for off-canvas menu systems. I’ll use that as an example
to illustrate the process of building a module.

## Building a module

To begin, I define three methods and a property that are used to manage the
interactions of the menu system.


    var jpm = {
        animated: true,
        openMenu: function( ) {
            …
            this.setMenuStyle( );
        },
        closeMenu: function( ) {
            …
            this.setMenuStyle( );
        },
        setMenuStyle: function( ) { … }
    };


The idea is to break down code into the smallest, most reusable bits possible.
I could have written just one`toggleMenu( )` method, but creating distinct
`openMenu( )` and `closeMenu( )` methods provides more control and reusability
within the module.

Notice that calls to module methods and properties from *within* the module
itself (such as the calls to`setMenuStyle( )`) are prefixed with the `this`
keyword—that’s how modules access their own members.

That’s the basic structure of a module. You can continue to add methods and
properties as needed, but it doesn’t get any more complex than that. After the
structural foundations are in place, the reusability layer—options and an
exposed API—can be built on top.

## jQuery plugins

The third aspect of well-designed code is probably the most crucial:
reusability. This section comes with a caveat. While there are obviously ways to
build and implement reusable components in raw JavaScript (we’re about 90
percent of the way there with our module above), I prefer to build jQuery
plugins for more complex things, for a few reasons.

Most importantly, it’s a form of unobtrusive communication. If you used
jQuery to build a component, you should make that obvious to those implementing
it. Building the component as a jQuery plugin is a great way to say that jQuery
is required.

In addition, the implementation code will be consistent with the rest of the
jQuery-based project code. That’s good for aesthetic reasons, but it also means
(to an extent) that developers can predict how to interact with the plugin
without too much research. Just one more way to build a better developer
interface.

Before you begin building a jQuery plugin, ensure that the plugin does not
conflict with other JavaScript libraries using the`$` notation. That’s a lot
simpler than it sounds—just wrap your plugin code like so:


    (function($) {
        // jQuery plugin code here
    })(jQuery);


Next, we set up our plugin and drop our previously built module code inside. A
plugin is just a method defined on the jQuery`($)` object.


    (function($) {
        $.jPanelMenu = function( ) {
            var jpm = {
                animated: true,
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };
        };
    })(jQuery);


All it takes to use the plugin is a call to the function you just created.


    var jpm = $.jPanelMenu( );


## Options

Options are essential to any truly reusable plugin because they allow for
customizations to each implementation. Every project brings with it a slew of
design styles, interaction types, and content structures. Customizable options
help ensure that you can adapt the plugin to fit within those project
constraints.

It’s best practice to provide good default values for your options. The
easiest way to do that is to use jQuery’s`$.extend( )` method, which accepts (
at least) two arguments.

As the first argument of `$.extend( )`, define an object with all available
options and their default values. As the second argument, pass through the
passed-in options. This will merge the two objects, overriding the defaults with
any passed-in options.


    (function($) {
        $.jPanelMenu = function(options) {
            var jpm = {
                options: $.extend({
                    'animated': true,
                    'duration': 500,
                    'direction': 'left'
                }, options),
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };
        };
    })(jQuery);


Beyond providing good defaults, options become almost self-documenting—
someone can look at the code and see all of the available options immediately.

Expose as many options as is feasible. The customization will help in future
implementations, and flexibility never hurts.

## API

Options are terrific ways to customize how a plugin works. An API, on the other
hand, enables extensions to the plugin’s functionality by exposing methods and
properties for the implementation code to take advantage of.

While it’s great to expose as much as possible through an API, the outside
world shouldn’t have access to all internal methods and properties. Ideally, you
should expose only the elements that will be used.

In our example, the exposed API should include calls to open and close the menu
, but nothing else. The internal`setMenuStyle( )` method runs when the menu
opens and closes, but the public doesn’t need access to it.

To expose an API, return an object with any desired methods and properties at
the end of the plugin code. You can even map returned methods and properties to
those within the module code—this is where the beautiful organization of the
module pattern really shines.


    (function($) {
        $.jPanelMenu = function(options) {
            var jpm = {
                options: $.extend({
                    'animated': true,
                    'duration': 500,
                    'direction': 'left'
                }, options),
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };

            return {
                open: jpm.openMenu,
                close: jpm.closeMenu,
                someComplexMethod: function( ) { … }
            };
        };
    })(jQuery);


API methods and properties will be available through the object returned from
the plugin initialization.


    var jpm = $.jPanelMenu({
        duration: 1000,
        …
    });
    jpm.open( );


## Polishing developer interfaces

With just a few simple constructs and guidelines, we’ve built ourselves a
reusable, extensible plugin that will help make our lives easier. Like any part
of what we do, experiment with this structure to see if it works for you, your
team, and your workflow.

Whenever I find myself building something with a potential for reuse, I break
it out into a module-based jQuery plugin. The best part about this approach is
that it forces you to use—and test—the code you write. By using something as you
build it, you’ll quickly identify strengths, discover shortcomings, and plan
changes.

This process leads to battle-tested code ready for open-source contributions,
or to be sold and distributed. I’ve released my (mostly) polished plugins as
open-source projects on[GitHub][5].

Even if you aren’t building something to be released in the wild, it’s
still important to think about the design of your code. Your future self will
thank you.

 [1]: https://twitter.com/addyosmani
 [2]: http://addyosmani.com/resources/essentialjsdesignpatterns/book/

 [3]: http://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript
 [4]: http://jpanelmenu.com/
 [5]: https://github.com/acolangelo
