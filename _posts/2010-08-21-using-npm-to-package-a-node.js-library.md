---
layout: article
title: Using npm to package a node.js library
categories: nodejs
updated_at: 2010-08-21
---
Goal
====

A getting started guide to npm for first time publishers.

Recommended Reading: http://github.com/isaacs/npm/blob/master/doc/developers.md

Pre-req: Install Node.js and npm
=======

via Ivy (pre-built binaries)
-------

    BASHRC=.bashrc # .bash_profile for OSX
    cd ~/
    curl -# http://github.com/creationix/ivy/raw/master/utils/setup.sh | sh
    PATH=~/ivy/bin:${PATH}
    cp ~/${BASHRC} ~/${BASHRC}.ivysave
    echo 'PATH=~/ivy/bin:${PATH}' >> ~/${BASHRC} # note the single quotes

via the-good-old-fashioned-way (takes 3 minutes or so to compile on an old 2-core machine)
--------

    cd ~/
    wget http://nodejs.org/dist/node-v0.2.0.tar.gz # or whatever the latest and greatest is
    tar xf node-v*
    cd node-v*
    ./configure
    make
    sudo make install
    
Publish with npm
=======

install npm
-------

    cd ~/
    curl http://npmjs.org/install.sh | sh


Add yourself to npm as a user
--------

    USERNAME=coolaj86
    PASSPHRASE="a long but easy-to-remember passphrase"
    EMAIL=coolaj86@email.com
    npm adduser ${USERNAME} "${PASSPHRASE}" ${EMAIL} # note the double quotes

Understanding your package layout
--------

A lot of this is arbitrary, but I'm assuming a basic convention.

    PROJECTNAME=futures
    PACKAGEDIR=/home/${USERNAME}/${PROJECTNAME}

    ${PACKAGEDIR}/lib # where your library goes
    ${PACKAGEDIR}/lib/${PROJECTNAME}.js # the main library goes
    ${PACKAGEDIR}/vendor # where packages you depend on (git-submodules) go 
    ${PACKAGEDIR}/package.json # tells `npm` about your app *NOT arbitrary*

Understanding `package.json`
---------

Recommended reading: http://github.com/isaacs/npm/blob/master/doc/json.md

**Don't put "js" or "node" in the name.**

`${PACKAGEDIR}/package.json`:

    {
      "name"          : "futures",
      "description"   : "Asynchronous programming aid for JavaScript. Works well with Node.js, Rhino, Underscore, jQuery, & PURE.",
      "url"           : "http://github.com/coolaj86/futures/",
      "keywords"      : ["util", "asynchronous", "futures", "promises", "subscriptions", "chaining", "server", "client", "browser"],
      "author"        : "AJ ONeal <coolaj86@gmail.com>",
      "contributors"  : [],
      "dependencies"  : [],
      "lib"           : "lib",
      "main"          : "./lib/futures",
      "version"       : "0.9.1"
    }

Publishing
---------

First test

    npm install ${PACKAGEDIR}/ # note the trailing slash
    mkdir ${PACKAGEDIR}/test -p
    echo "require('${PROJECTNAME}');" > ${PACKAGEDIR}/test/npm-test.js
    node ${PACKAGEDIR}/test/npm-test.js

Then publish

    VERSION=0.9.1

    npm publish ${PACKAGEDIR}/
    npm tag ${PROJECTNAME}@${VERSION} stable # this version will auto-install rather than another version

Undoing
-------

    npm unpublish ${PROJECTNAME}@${VERSION}
    # you may want to retag an older version as stable
    # npm tag ${PROJECTNAME}@${OLD_VER} stable
    
