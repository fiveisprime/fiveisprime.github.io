---
layout: post
title: Different Ways to Declare Functions
---

There are a few different ways to declare a function in JavaScript (function
definitions, function expressions, and methods) and each has its own set of
strengths and weaknesses. This is a simple overview of the differences between
function definitions and expressions.

## Function definition
Function definition is most like other languages. I came from a background in
C# so this made a lot of sense to me starting out with JavaScript.

    function foo() {
        // Do things.
        foo();
    }

* Not anonymous
* Shows up named in the stack trace
* Not _hoisted_
* Allows recursive calls and is available outside of function scope


## Function expression
Function expressions show off a strength of JavaScript - assigning functions to
variables. Again, as a C# developer, this was a bit awkward at first, but is now
one of my preferred methods for declare a function (unless recursion is
necessary).

    var bar = function() {
        // Do things.
    }

* Is technically anonymous
* Shows up in stack trace as (Anonymous function)
* Hoisted
* Unable to call itself recursively but available outside of function scope

## Named function expression
This is a mixture of the two. Function declarations are named which means that
they show up in a stack trace as the name of the function rather than
`Anonymous`. Using a named function expression lets you use the function as a
variable and you get the benefit of a function declaration.

    var baz = function baz() {
        // Do things.
        baz();
    }

* Not anonymous
* Shows up named in the stack trace
* Hoisted
* Able to call itself recursively

---

Function expressions may be used conditionally:

    var foo
      , handler = App.createHandler();

    if (response) {
        foo = handler.foo;
    } else {
        foo = function() {
            // Some default functionality...
        }
    }

    foo();
