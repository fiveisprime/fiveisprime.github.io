---
layout: post
title: Using Make with Node.js
---

There are some great tools for automating client-side tasks such as
[Grunt](http://gruntjs.com/) and [Gulp](http://gulpjs.com/), but what if all you
want to do is automate the lint and test that is already part of your workflow?

You already use JSHint's command line tool along with mocha and you just want to
automate this process to work with a continuous integration service such as
[codeship](http://codeship.io) or even [travis](http://travis-ci.org/).

### Using Make

You can very quickly do this using Make. First, ensure that all of your test
tools are installed as dev dependencies. For this example, I'm using JSHint,
Mocha, Should, and Istanbul:

    npm install jshint mocha should istanbul -D

This will install all of the modules and save them in the `devDependencies`
object of your `package.json`.

Now create a Makefile that looks about like this:

    SRC = $(wildcard *.js)

    test: $(SRC)
      @node node_modules/.bin/jshint $^
      @node node_modules/.bin/istanbul test node_modules/.bin/_mocha \
      -R spec -- \
      --require should \
      --reporter spec

> Be sure to use tabs when creating your Makefile!

The Makefile is simply running the commands listed in the `test` target and will
only execute the Istanbul/mocha tests if the lint passes (if the process exits
with 0).

>  This target will run tests in a directory named `spec`, if your tests are in a
>  directory named `test`, you will need to change the `-R spec -- \` line to
>  `-R test -- \` and append `.PHONY: test` to the file so that make doesn't try to
>  build your test files directly.

Now you can add the test script to your package.json

    "scripts": {
      "test": "make test"
    }

Running `npm test` will run your tests, but even better is that, because
Istanbul is being used, you can add the coverage option to generate a code
coverage report.

    $ npm test
    > make test
     ...
    
    $ npm test --coverage
    > make test
    
    ========================= Coverage summary =========================
    Statements   : 100% ( 39/39 )
    Branches     : 100% ( 2/2 )
    Functions    : 100% ( 16/16 )
    Lines        : 100% ( 37/37 )
    ====================================================================

> Your `SRC` variable may need to be updated depending on the structure of your
> module.

### Thoughts

Make is a quick and easy way to automate the workflow of your node.js module.
Linting, running tests, and even generating code coverage reports is super easy
- all in less than 10 lines of code. :)
