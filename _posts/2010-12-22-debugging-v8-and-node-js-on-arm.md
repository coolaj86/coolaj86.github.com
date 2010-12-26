---
layout: article
uuid: C38B6D0D-1477-409E-BB81-C0DA16405456
title: Debugging V8 and Node.JS on ARM
name: debugging-v8-and-node-js-on-arm
created_at: 2010-12-22
updated_at: 2010-12-22
categories: nodejs
---

Building V8 and Node.JS
====

I'm not too familiar with how to go about sending build options through Node.JS's build system to v8,
so I'll build them separately with V8 as a shared library.

This process will take an hour or two (mostly wait time) and you probably won't get by without 1.5GB of disk space and 256mb RAM.

If you have a little less than 256mb (say 160mb), you might try adding just a bit of swap temporarily.

    dd if=/dev/zero of=/128mb.swp bs=1M count=128
    mkswap -f /128mb.swp
    swapon /128mb.swp

Note: In the instructions I show how to natively compile V8 and Node.JS for debugging on ARM.
The debugging options are rather blatent. If you simply omit them you will have a release system.
The CCFLAGS are specific to ARM.

build tools
----

Odd that you'd have to use one intepreted languaged to compile an intepreter and frontend... but `scons` and `waf` are just that way...

    opkg install task-sdk-native subversion git
    opkg install \
      python \
      python-distutils \
      python-modules \
      python-misc

scons
----

Browse the [scons downloads](http://www.scons.org/download.php) for the version of your choice.

I'll use the latest production release.

    cd ~/
    SCONS_VER=2.0.1
    wget http://prdownloads.sourceforge.net/scons/scons-${SCONS_VER}.tar.gz
    tar xf scons-${SCONS_VER}.tar.gz
    cd scons-${SCONS_VER}
    python setup.py install
    

V8
----

    cd ~/
    svn checkout http://v8.googlecode.com/svn/trunk/ v8-read-only
    cd v8-read-only
    DBGFLAGS="-ggdb3 -g3 -O0"
    export CCFLAGS="-march=armv7-a -mtune=cortex-a8 -mfpu=neon -mfloat-abi=softfp -mthumb-interwork -mno-thumb ${DBGFLAGS}"
    scons mode=debug snapshot=on library=shared importenv=CCFLAGS
    cp libv8_g.so /usr/local/lib

Since this takes such a ridiculously long time, you may wish to `tar` your build for later use.

Node.JS
----

    cd ~/
    NODE_VER=v0.2
    git clone git://github.com/ry/node.git
    cd node
    git checkout ${NODE_VER}

    # Node looks for the normal version of v8 as well.
    # This is a bug, but easy to work around
    cd /usr/local/lib
    ln -s libv8_g.so libv8.so

    # Note that you can't use a relative path for the include directory
    # Note that the '_g' is added automatically, this is common convention for debug files
    DBGFLAGS="-ggdb3 -g3 -O0"
    export CCFLAGS="-march=armv7-a -mtune=cortex-a8 -mfpu=neon -mfloat-abi=softfp -mthumb-interwork -mno-thumb ${DBGFLAGS}"
    ./configure \
      --prefix=/usr/local \
      --debug \
      --shared-v8 \
      --shared-v8-includes=/home/root/v8-read-only/include \
      --shared-v8-libpath=/usr/local/lib \
      --shared-v8-libname=v8

    make
    make install

Debugging
----

    opkg install gdb

First you must create a gdb script (otherwise this gets real old, real fast)

`my-test-case.gdb.scr:`

    run /home/root/tests/my-test-case.js --some args --can=go --here

And then a script to run gdb with the script perhaps

`run-my-test-case.sh`

    #!/bin/sh
    cd /home/root/tests/
    gdb node --command=./my-test-case.gdb.scr
