---
layout: post
title: Getting Remote IP Through a Proxy with Express
---

With express, there is an easy way to get a user's IP address from a request
object `req.remoteAddres`, but you will find that this will not work when
hosting your application on a PaaS provider. The remote address will always be
the same IP which is the platform's load balancer.

In order to get the user's IP address, you'll have to check the forwarded for
header which will include the the remote IP address as well as the internal IP
address in a comma separated string so you'll have to parse out the address:

    var userIP = req.headers['x-forwarded-for'].split(',')[0];
