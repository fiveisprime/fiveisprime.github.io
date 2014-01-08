---
layout: post
title: Deploying a Modulus Project From Travis
---

You'll need the following (or make sure they are up to date):

- [Modulus CLI](https://npm.im/modulus) (v0.2.3 as of this post)
- [Travis CLI](http://rubygems.org/gems/travis) (v1.6.6 as of this post)

Log into Modulus CLI then create a token:

    $ modulus token create
    Welcome to Modulus
    You are logged in as fiveisprime
    [✓] Token: 4e287c37-2e7d-416f-bf53-dab7d564c262

The Modulus CLI checks for the `MODULUS_TOKEN` environment variable that is used
to run commands without running the login command first. Obviously, you won't
want this token to be visible in your `.travis.yml` configuration file so you'll
need to encrypt it using the travis CLI.

    $ travis encrypt MODULUS_TOKEN=4e287c37-2e7d-416f-bf53-dab7d564c262

This will output a secure variable -- something like `secure: "..."`. If you
already have a `.travis.yml` file, you can include the `--add` option to add
the variable to your configuration file automatically.

If you don't already have a configuration file, create a file named
`.travis.yml` in the root of your repository and paste the following into the
file:

    language: node_js
    node_js:
      # Include which versions of node to test against here.
      - 0.8.x
      - 0.10.x
    env:
      global:
        # Replace the next line with the output from travis.
        - secure: "..."
    after_success:
      - npm install -g modulus
      - modulus deploy -p "my project name"

Notice the `env:global:secure` property which is where you will copy your
encrypted token from travis.

This configuration will run the `npm test` script against node versions 0.8.x
and 0.10.x then, if successful, install the Modulus CLI and deploy your
application.
