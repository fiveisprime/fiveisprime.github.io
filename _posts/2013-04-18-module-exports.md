---
layout: post
title: exports or module.exports?
---

Node modules are sandboxed and exposed using either `module.exports` or
`exports`. If you have written any kind of module then you have used this
mechanism before, but what is the difference between `exports` and
`module.exports` and which should you use?

## tl;dr

Use `exports` to attach members; `module.exports` to override the return type.

### Figuring out the difference

Let's start with two files in the same directory: `exports.js` and `app.js`.

> _exports.js_

    exports = function() { console.log('Hey there'); }

> _app.js_

    var test   = require('./exports')
      , assert = require('assert')

    assert.strictEqual(
      typeof test
    , 'function'
    , 'should return a function');

Run the above using `node app.js` and you will see that the following error is
thrown:

    AssertionError: should return a function

This happens because changing what `exports` _is_ (changing the pointer)
actually disconnects the exports variable from the Node.js environment. Updating
`app.js` makes this a little clearer:

> _app.js_

    var test = require('./exports')
      , util = require('util');

    console.log(typeof test);
    console.log(util.inspect(test));

Which outputs the following when run:

    object
    {}

What's actually being returned by our module is `module.exports` - this is why
the return type is `object` rather than `undefined`. Let's update `exports.js`.

    module.exports = function() { console.log('Hey there'); }

Run `app.js` again which will change the output to something that makes more
sense:

    function
    [Function]

### What's going on here?

Fortunately for all of us, the source for Node.js is available so that you can
figure this part out. In
[/src/node.js](https://github.com/joyent/node/blob/master/src/node.js) you can
see that your code is wrapped in a closure and passed both `exports` *and*
`module`. Of course, further inspection will show you that `exports` contains a
pointer to `module.exports` and suddenly everything makes sense.

*Overwriting exports overwrites the pointer to module.exports which disconnects
exports from the Node.js environment!*

###What's the point?
Exports is a helper function that points to `module.exports`. This is meant to
make your life easier. That is all. Use it to expose functions of your module,
but if your module needs to replace what is exposed, you *must* use
`module.exports`.
