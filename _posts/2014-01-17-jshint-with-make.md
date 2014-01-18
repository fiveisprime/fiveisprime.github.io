---
layout: post
title: JSHint with Make
---

I talked about [using make with node](/2014/01/02/using-make-with-node/), but
this is specific to the JSHint configuration/target for make.

## .jshintrc

One of my favorite things about JSHint is how easy it is to configure the
rules. I have an rc file that I typically use (this changes a little when I'm
mixing client and server JavaScript).

This goes in the root of the directory alongside the Makefile.

<script src="https://gist.github.com/fiveisprime/6128393.js"></script>

This has the `laxcomma` and `expr` relaxing options turned on because I'm that
kind of person. Also included are the globals for Mocha.

## reporter

The default JSHint reporter is lacking. Mostly in the fact that, if the lint
succeeds, there is not output, but also because it's just plain ugly and not
too easy to read. Fortunately, our friend
[Sindre](https://github.com/sindresorhus) solved this problem with
[JSHint-Stylish](https://github.com/sindresorhus/jshint-stylish) which formats
errors in a **much** better way and also lets you know when the lint succeeded.

## Makefile

The actual lint target in make

    lint: $(SRC)
      @node_modules/.bin/jshint \
      --reporter node_modules/jshint-stylish/stylish.js \
      $^

This relies on all of the source files being declared in a variable named
`SRC`. I like to do this just to make sure that the target is clean and pretty.
You can use `$(wilcard dir/*.js)` to ensure that all files in a directory are
included in the lint.

There is no need to specify the rc file because JSHint checks the current
working directory for a `.jshintrc` to use.
