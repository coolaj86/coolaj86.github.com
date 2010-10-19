---
layout: article
uuid: 79f25c6d-9c3d-4563-bfc8-84533388b855
title: CouchDB on Gumstix with OpenEmbedded
name: couchdb-on-gumstix-with-openembedded
created_at: 2010-10-07
updated_at: 2010-10-07
categories: gumstix
---

Goal
====

Gumstix, like BeagleBoard, is an embedded platform. I want to run CouchDB on it.

Building CouchDB on Gumstix
====

**STATUS**:

  * Native build: Works!
  * Cross-compile build: haven't tested. See the **resources** section at the bottom. Looks "simple" enough.

Native Installation
====

We'll start by building on ARM and later move to a bitbake recipe (if it's possible for me to create one).

    opkg install task-sdk-native

nspr
---

About 5 mins. Installs libraries to `/usr/local`

As per [Mozilla's NSPR build instructions](https://developer.mozilla.org/en/NSPR_build_instructions)


    cd ~/
    cvs -q -d :pserver:anonymous@cvs-mirror.mozilla.org:/cvsroot co -r NSPR_4_8_RTM mozilla/nsprpub
    mkdir target
    cd target
    ../mozilla/nsprpub/configure --disable-debug --enable-optimize
    make
    sudo make install

This is also available via bitbake, but that method didn't work for me -- see the appendix.

SpiderMonkey
----

About 10 mins. Installs libraries to `/usr/local`

OpenEmbedded doesn't stock Mozilla's SpiderMonkey. That must be built first.

    cd ~/target
    wget http://ftp.mozilla.org/pub/mozilla.org/js/js-1.8.0-rc1.tar.gz
    tar xf js-1.8.0-rc1.tar.gz -C
    cd js/src
    # if nspr is made from source
    make JS_DIST=/usr/local JS_THREADSAFE=1 BUILD_OPT=1 -f Makefile.ref > /dev/null
    sudo make install

    # or if made from bitbake
    # make JS_DIST=/usr JS_THREADSAFE=1 BUILD_OPT=1 -f Makefile.ref > /dev/null
    # or without thread support for couchdb
    # make BUILD_OPT=1 -f Makefile.ref > /dev/null

`makefile`:

    BLD := Linux_All_OPT.OBJ
    PREFIX := /usr/local
    #PREFIX := /usr

    install:
        cp ${BLD}/libjs.so ${PREFIX}/lib
        cp ${BLD}/js ${PREFIX}/bin
        cp ${BLD}/jscpucfg ${PREFIX}/bin
        cp ${BLD}/jskwgen ${PREFIX}/bin
        mkdir -p ${PREFIX}/include/js
        cp *.h ${PREFIX}/include/js
        cp *.tbl ${PREFIX}/include/js
        cp ${BLD}/*.h ${PREFIX}/include/js

Create the makefile above and then run

    sudo make install

other
---

    sudo opkg install libgnutls26 icu icu-dev curl curl-dev 

erlang
----

    USER=root
    GUMSTIX=192.168.1.10

    cd ~/overo-oe
    bitbake erlang
    scp ./tmp/deploy/glibc/ipk/armv7a/erlang-libs_*_armv7a.ipk ${USER}@${GUMSTIX}:~/
    scp ./tmp/deploy/glibc/ipk/armv7a/erlang_*_armv7a.ipk ${USER}@${GUMSTIX}:~/

On the overo

    sudo opkg install ~/erlang*

CouchDB
----

About 15 mins. Installs executable to `/usr/local`

    wget http://www.ecoficial.com/apachemirror//couchdb/1.0.1/apache-couchdb-1.0.1.tar.gz
    tar xf apache-couchdb-1.0.1.tar.gz
    cd apache-couchdb-1.0.1
    ./configure
    make && sudo make install

Then configuration

    cd ~/
    sudo su -

    adduser couchdb
    vim /etc/passwd
    chown couchdb:couchdb /usr/local/var/lib/couchdb/

    chown -R couchdb: /usr/local/var/*/couchdb /usr/local/etc/couchdb
    chmod 0770 /usr/local/var/*/couchdb
    chmod 664 /usr/local/etc/couchdb/*.ini
    chmod 775 /usr/local/etc/couchdb/*.d

    cd /etc/init.d
    ln -s /usr/local/etc/init.d/couchdb couchdb

    update-rc.d couchdb defaults

    /etc/init.d/couchdb start
    ping -c 1 127.0.0.1 # just to make sure that you haven't misconfigured `/etc/networking/interfaces` ... I have
    curl http://127.0.0.1:5984/
    # {"couchdb":"Welcome","version":"1.0.1"}

    # run test suite in firefox at http://127.0.0.1:5984/_utils/couch_tests.html?script/couch_tests.js

Admin Party!!!!
====

Yes, party because it's working.

Yes, go into the browser and set a password.

http://${GUMSTIX}:5984/_utils

Replication
----

I also have an account on [couchone.com](http://couchone.com) which uses http authentication.

To replicate the database I:

  * create a new database on my gumstix
  * secure it by selecting both one admin and one reader
  * replicate it by entering the remote as `http://USER:PASSWORD@mydb.couchone.com`
  * selecting the name of my local database

Resources
====

  * [Mozilla.org: NSPR Build Instructions](https://developer.mozilla.org/en/NSPR_build_instructions)
  * [Mozilla.org: SpiderMonkey Build Documentation](https://developer.mozilla.org/en/SpiderMonkey_Build_Documentation)
    * [Mozilla.org: SpiderMonkey Linux Prerequisites](https://developer.mozilla.org/En/Developer_Guide/Build_Instructions/Linux_Prerequisites)
  * [SpiderMonkey on ARM (CROSS_COMPILE)](http://software.itags.org/mozilla/83286/)
    * [CROSS_COMPILE SpiderMonkey](http://old.nabble.com/about-SpiderMonkey-cross-compile-td21085220.html)
  * [CouchDB on Android](http://github.com/apage43/couch-android-launcher/wiki/couchdb-build-notes-dump)
  * [CouchDB on Ubuntu](http://wiki.apache.org/couchdb/Installing_on_Ubuntu)

Appendix
====

`nspr` is not available through the `Angstrom` repository. It can be bitbaked.

    USER=root
    GUMSTIX=192.168.1.110

    cd ~/overo-oe
    bitbake nspr
    scp ./tmp/deploy/glibc/ipk/armv7a/nspr_*-*_armv7a.ipk ${USER}@${GUMSTIX}:~/
    scp ./tmp/deploy/glibc/ipk/armv7a/nspr-dev_*-*_armv7a.ipk ${USER}@${GUMSTIX}:~/
    scp ./tmp/deploy/glibc/ipk/armv7a/nspr-static_*-*_armv7a.ipk ${USER}@${GUMSTIX}:~/

    sudo opkg install ~/nspr_*.ipk
    
Errors
----

Missing nspr components - try installing from source rather than bitbake

    In file included from jsatom.h:53,
                     from jsapi.c:57:
    jslock.h:45:20: error: pratom.h: No such file or directory
    jslock.h:46:20: error: prlock.h: No such file or directory
    jslock.h:47:20: error: prcvar.h: No such file or directory
    jslock.h:48:22: error: prthread.h: No such file or directory
    In file included from jsatom.h:53,
                     from jsapi.c:57:
    [HUGE NUMBER OF ERRORS OMITTED HERE]
    jsapi.c: In function 'JS_NewStringCopyZ':
    jsapi.c:5251: error: 'JSRuntime' has no member named 'emptyString'
    jsapi.c: In function 'JS_NewUCStringCopyZ':
    jsapi.c:5293: error: 'JSRuntime' has no member named 'emptyString'
    make[1]: *** [Linux_All_OPT.OBJ/jsapi.o] Error 1
    make: *** [all] Error 2

Missing nspr and SpiderMonkey

    configure: error: Could not find the js library.
    
    Is the Mozilla SpiderMonkey library installed?

Missing ICU

    checking for icu-config... no
    *** The icu-config script could not be found. Make sure it is
    *** in your path, and that taglib is properly installed.
    *** Or see http://ibm.com/software/globalization/icu/
    configure: error: Library requirements (ICU) not met.

Missing link to libicuuc.so

    checking for icu-config... /usr/bin/icu-config
    ### icu-config: Can't find /usr/lib/libicuuc.so - ICU prefix is wrong.
    ###      Try the --prefix= or --exec-prefix= options 
    ### icu-config: Exitting.
    checking for ICU >= 3.4.1... expr: syntax error
    can't find ICU >= 3.4.1
    configure: error: Library requirements (ICU) not met.

Missing curl-dev

    checking for curl-config... 

Missing icu-dev

    unicode/ucol.h

Missing gnutls

    gcc: /usr/lib/.libs/libgnutls.so: No such file or directory
