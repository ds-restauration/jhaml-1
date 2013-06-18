jhaml.js
========
Jhaml.js is a JavaScript implementation of Joe's HTML abstraction markup language, a domain specific language for HTML templating by Joe Langeway.

You can use it. The license is the MIT license.

Jhaml was developed with input from Jamal Williams and Clint Zehner to reduce the tedium of writing copious HTML templates.

What Jhaml is
-------------
Jhaml is a notation for defining named templates which can call to each other like functions, passing named arguments with dynamic scope. Compiling some jhaml notation results in template functions becoming available on a templates object. Other template functions on that object can be called by jhaml templates as though they were also notated in jhaml. A particular application may run many independent instances of jhaml/jhaml-templates-objects.

What Jhaml.js is
----------------
Jhaml.js is a JavaScript implementation of the jhaml notation. It is structured as a require.js module which exports one constructor. To make a jhaml.js object simply "new" that constructor. The resulting jhaml.js object will have a templates object as a property and a compile method. Feed jhaml notation to the compile method, and template functions show up on the templates object. See jhaml_tests.html and jhaml_demo.html for example usage.

What Jhaml is and is not for
---------------------
Jhaml is intended to solve the problem of quickly generating and maintaining HTML structure. It does not support iteration or alternation of any kind. It purposefully does not make it easy to put policy in templates. It is intended to sit on top of a view model of some sort which will contain any such logic.

It defines functions which return strings. It does not interact with the DOM. It does not update in place.

There are lots of JavaScript frameworks. This isn't one of them. It's a DSL for making HTML and it's an opinionated template engine.

Brief tutorial
--------------
See jhaml_tests.js and jhaml_demo.js for the big picture. Here is a quick introduction to the notation.

Template functions are defined by "def" statements. So this:

    def fDiv { div }

is equivalent to this:

    jhaml.templates.fDiv = function(args) { return '<div></div>'; };

HTML tags are defined by tag names optionally followed by id's, classes, or attributes just as they would be given in a css query. So this:

    li#listItem1.style1.style2[data-item="1"]

would result in HTML like this:

    <li id="listItem1" class="style1 style2" data-item="1"></li>

Tags are given contents with curly braces. So this:

    div { span }

would result in HTML like this:

    <div><span></span></div>

Literal HTML can be included between tildes like this:

    div.wrap { span.text { ~Text content~ } }

Consecutive tags or literals are siblings. So this:

    div.wrap { p { ~paragraph 1~ } p { ~paragraph 2~ ~more~ } }

would result in HTML like this:

    <div class="wrap"><p>paragraph 1</p><p>paragraph 2more</p></div>

Arguments can be referenced with an @ sign. So this:

    def fDiv { div { @content.html } }

is equivalent to this:

    jhaml.templates.fDiv = function(args) { return '<div>' + args.content.html + '</div>'; };

Arguments can be references and HTML escaped by including a tilde between the @ and the first argument name. So this:

	def fText { p.text { @~text } }

is equivalent to this:

    jhaml.templates.fText = function(args) { return '<p class="text">' + htmlEscape(args.text) + '</p>'; };

where htmlEscape() does what you'd think it does.

References can be used for id's and classes and attribute values like so:

    div#@id.@class1.@class2[data-foo=@foo]

Parenthesis can be used when references and multiple class names would be ambiguous like so:

	def.style1.@(classes.class1).style3

You can call into other templates by giving their name followed by a list of named arguments in parenthesis like this:

    def caller { div { callee() } }
    def caller { div { callee(text: ~a literal for an argument~) } }
    def caller { div { callee(content: { span { ~a block for an argument~ } } ) } }
    def caller { div { callee(arg: @a.reference, arg2: "a second argument in a quote") } }

White space is totally meaningless and may be omitted or abused except inside literals and quotes where it is exactly preserved.

Comments may appear anywhere a tag may or before argument names in a call or after argument values in a call. They begin with "/*" and end with "*/" and may nest. They are not respected inside quotes or literals. Like so:

    def caller { dev { callee(
            /* no arguments 
                /* really none */ 
            */
            ) /*end call to callee */ 
        }
    }



