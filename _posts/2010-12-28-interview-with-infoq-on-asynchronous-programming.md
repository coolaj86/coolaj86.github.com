---
layout: article
uuid: C031D249-13BC-4ED9-A295-309A07289F6C
title: Interview with InfoQ on Asynchronous Programming
name: interview-with-infoq-on-asynchronous-programming
created_at: 2010-12-28
updated_at: 2010-12-28
categories: thesystemisntdown
---

Interview with InfoQ
====

Today I received an e-mail from [InfoQ](http://infoq.com) about [FuturesJS](https://github.com/coolaj86/futures).

> Hi AJ,
> 
> My name is Dionysios Synodinos, I'm an editor for InfoQ and since we're doing an article on the challenges of asynchronous programming in JavaScript, we'd love to have your feedback, especially regarding the futures library.
> 
> Here are the questions:
> 
>    1. What problems does the library address? Ie. is it mostly about removing boilerplate code and manual callback handling from async I/O, or does it also provide orchestration or other functionality.
>    2. Does the library implement ideas from CS research?
>    3. Does the library offer any error handling strategies? How does it interact with exception handling?
>    4. Was the library inspired/influenced by the work done with F#'s Workflows, Rx (the Javascript version), or other projects?
>    5. Are there any new Javascript language features or changes that could make the library better, eg. allow to be more concise, etc?
> 
> I understand that you are a very busy person and I appreciate your time!
> 
> Cheers,
> Dio

What problems does the library address?
====

Asynchronous and event-driven programming can be somewhat difficult to reason about.

I created Futures primarily to

  * provide a single asynchronous control-flow library for Browser and Server-Side (Node.JS) use.
  * expose a consistent pattern for handling callbacks and errbacks
  * control the flow of an application in which events depend on one another
  * handle callbacks for multiple resources, such as mash-ups
  * encourage good programming practices such as using models and handling errors

`Futures.future` and `Futures.sequence` simply reduce the amount of common boilerplate code and provide some flexibility.

`Futures.join` can join (in similar fashion to how one would join threads) or synchronize (for events which occur at intervals) multiple futures.

`Futures.chainify` makes it easy to create asynchronous models, similar to the Twitter Anywhere API.


Does the library implement ideas from CS research?
====

Yes. The biggest influences have been

  * [Google's How to think about OO](http://googletesting.blogspot.com/2009/07/how-to-think-about-oo.html)
  * [Yahoo!s Crockford on JavaScript Act III](http://yuiblog.com/crockford/)
  * [Google's The Lazy Programmers Guide to Secure Computing](http://www.youtube.com/user/GoogleTechTalks#p/search/7/eL5o4PFuxTY)

The best thing about asynchronous programming is that it naturally leads to writing more module code and 
if you're going to have any sort of an asynchronous model you're forced to follow the principle of always
passing parameters in and never passing data which belongs to a model outside of that model.

Does the library offer any error handling strategies? How does it interact with exception handling?
====

Since exceptions can't be "thrown" asynchronously, instead the user is encouraged to pass any exceptions as the first argument to the callback.

The basic idea is to `try {} catch(e) {}` the error and pass it rather than stopping the application at some unrelated time.

`Futures.asyncify()` does this for the purpose of using synchronous functions in a predominantly asynchronous environment.

Here's an example:


    (function () {
      "use strict";

      var Futures = require('futures'),
        doStuffSync,
        doStuff;

      doStuffSync = function () {
        if (2 % Math.floor(Math.random()*11)) {
          throw new Error("Some Error");
        }
        return "Some Data";
      };

      doStuff = Futures.asyncify(doStuffSync);

      doStuff.whenever(function (err, data) {
        if (err) {
          console.log(err);
          return;
        }
        console.log(data);
      });

      doStuff();
      doStuff();
      doStuff();
      doStuff();
    }());


Was the library inspired/influenced by the work done with F#'s Workflows, Rx (the Javascript version), or other projects?
====

Not at first, no.

I was building a mash-up site using Facebook and Amazon and my first attempt was a mess because
I just didn't understand how to go about handling a model made from two resources.
(I wasn't really too familiar with JavaScript at the time; I had been trial-and-error-ing through the "[WTFJS](http://wtfjs.com/)"es and using a little jQuery to ease the DOM pain)

What I found was that it is easier to always assume that any data may take some time to retrieve than to ever assume that the data will exist when needed and then have to refactor everything down the entire chain when any one particular dataset can only be handled asynchronously.

I tried, failed, and half-succeeded using a few different methods and then, fortunately, someone on [my local JavaScript User Group list](https://groups.google.com/d/msg/ujsug/NBddMI5TV9M/14TI4HSYK2wJ) made mention of the [Crockford on JS lecture series](http://yuiblog.com/crockford). After watching the whole series (I watched the 3rd section at least 3 times) I finally had a better grasp on how to manage the "problems" (or rather, opportunities) of asynchronous programming so I found Crockfords slides and started with the promises example that he gave as my base point.

Later on I started playing with [Node.JS](http://nodejs.org) and that's when I changed my error-handling strategy
(but not the documentation hasn't relflected that until just a few days ago).
In Futures 2.0, which I'll be releasing this upcoming Sunday, I've also bundled Node.JS's EventEmitter for Browser use.

Are there any new Javascript language features or changes that could make the library better, eg. allow to be more concise, etc?
====

The most repeated code in the library is the fudge that makes the same code work in the Browser and Node.JS.
I know some libraries (such as teleport) have popped up to try to solve this issue, but I haven't played with any of them yet.
It certainly would be nice if an asynchronous `require` had been built into the the language.

From my point of view, a language with as naturally asynchronous as JavaScript should have something akin to Futures built-in.

Although CommonJS has [a few proposals](http://wiki.commonjs.org/wiki/Promises) for standardizing server-side Promises,
but whereas their focus is more an data privacy, Futures is more focused on control-flow, end-developer ease-of-use, and browser compatibility.
