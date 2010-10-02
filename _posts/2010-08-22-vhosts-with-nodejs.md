---
layout: article
uuid: 74ecc210-fc0e-4447-afe3-8db9216ebc1e
title: vhosts with nodejs
categories: nodejs
updated_at: 2010-08-22
---
Goal
====

Show 3 applications running on the same host.

todo
----

  * get an app beyond-the basics

Pre-reqs:
----

  * node v0.2.0
  * npm
  * connect

Procedure
=====

Create some virtual hostnames:

`/etc/hosts`

    127.0.0.1 localhost
    127.0.0.1 hello
    127.0.0.1 bye

or if your development box is public and online, it may have an IP such as

    74.125.19.147 remotehost
    74.125.19.147 hello
    74.125.19.147 bye

or if you setup DNS for breakfast and take it back down for lunch just do your own thing...
    

Install connect:

    npm install connect

Grab the [test application][vhost-app]:

[vhost-app]: http://github.com/senchalabs/connect/raw/master/examples/vhost/app.js

Note that the names of the `vhosts` and the `port numbers` are arbitrary.

It is important that *the `vhost` names should match the `/etc/hosts` names*

`~/node-vhost/vhosts.js`:

    /**
     * Module dependencies.
     */

    var connect = require('connect'),
      host, hello, bye;

    host = connect.vhost('peru', connect.createServer(function(req, res){
        res.writeHead(200, {});
        res.end('local (vhost)');
    }));

    hello = connect.vhost('hello', connect.createServer(function(req, res){
        res.writeHead(200, {});
        res.end('Hello World (vhost)');
    }));

    bye = connect.vhost('bye', connect.createServer(function(req, res){
        res.writeHead(200, {});
        // This will be caught and handled by connect,
        // which means doesn't cause the other servers to crash!
        throw Error('Goodbye World!!! AGH!!!!!');
        res.end('Goodbye World (vhost)');
    }));

    connect.createServer(
        // Shared middleware
        connect.logger(),
        host,   // http://host:7886 server with own middleware
        hello,  // http://hello:7886 server with own middleware
        bye     // http://bye:7886 server with own middleware
    ).listen(7886);


Run the app:

    cd ~/node-vhost/
    node vhosts.js

Visit it in your browser:

    http://hello:7886
    http://bye:7886

Adding Applications as Applications
======

`~/node-apps/hello.js`:

    var connect = require('connect');

    exports.hello = function(req, res){
        res.writeHead(200, {});
        res.end('Hello World (vhost)');
    };

`~/node-apps/bye.js`:

    var connect = require('connect');

    exports.bye = function(req, res){
        res.writeHead(200, {});
        // This will be caught and handled by connect,
        // which means doesn't cause the other servers to crash!
        throw Error('Goodbye World!!! AGH!!!!!');
        res.end('Goodbye World (vhost)');
    } 


`~/node-vhost/vhosts.js`:

    var connect = require('connect'),
      hello_app = require('../node-apps/hello').hello,
      bye_app = require('../node-apps/bye').bye,
      host, hello, bye;

    host = connect.vhost('peru', connect.createServer(function(req, res){
        res.writeHead(200, {});
        res.end('local (vhost)');
    }));

    hello = connect.vhost('hello', connect.createServer(hello_app));
    bye = connect.vhost('bye', connect.createServer(bye_app));

    connect.createServer(
        // Shared middleware
        connect.logger(),
        host,   // http://host:7886 server with own middleware
        hello,  // http://hello:7886 server with own middleware
        bye     // http://bye:7886 server with own middleware
    ).listen(7886);

Run it again:
-----

    cd ~/
    node node-vhosts/vhosts.js

You should get the same results.
The upshot to this is that you're running multiple applications in one process.
The downshot to this is that you have to restart all applications to add a new one.

Remember, this app is just like a browser app. It's not like a Rails, Django, Pylons, or PHP app. Everything is still event driven just like in the browser.

>  An http request to this server is roughly equivalent to a button click in the browser. Both stateless. Both event-driven.
