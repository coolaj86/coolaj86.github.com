---
layout: article
uuid: 79f25c6d-9c3d-4563-bfc8-84533388b855
title: CouchDB on Gumstix with OpenEmbedded
name: couchdb-on-gumstix-with-openembedded
created_at: 2010-10-07
updated_at: 2010-10-07
categories: gumstix
---

Building CouchDB on Gumstix
====

Gumstix, like BeagleBoard, is an embedded platform. I want to run CouchDB on it.

This blog is incomplete and will probably take me several weeks to complete. Please drop comments if you have hints.

Native Installation
====

We'll start by building on ARM and later move to a bitbake recipe (if it's possible for me to create one).

    opkg install task-sdk-native

nspr
---

About 5 mins. Installs libraries to `/usr/local`

    cvs -q -d :pserver:anonymous@cvs-mirror.mozilla.org:/cvsroot co -r NSPR_4_8_RTM nsprpub
    mkdir target
    cd target
    ../mozilla/nsprpub/configure --disable-debug --enable-optimize
    make
    sudo make install

This is also available via bitbake -- see the appendix.

SpiderMonkey
----

About 10 mins. Installs libraries to `/usr/local`

OpenEmbedded doesn't stock Mozilla's SpiderMonkey. That must be built first.

    sudo opkg install nspr # or bitbake nspr on your host 
    wget http://ftp.mozilla.org/pub/mozilla.org/js/js-1.8.0-rc1.tar.gz
    tar xf js-1.8.0-rc1.tar.gz -C
    cd js/src
    # if made from source
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

ICU
---

About 1 min. Available in the Angstrom repos.

    sudo opkg install icu

Fix non-existent links:

    cd /usr/lib
    ls libicu* | grep '.0' | while read ICU; do sudo ln -s ${ICU} `basename ${ICU} .36.0` ; done

curl
----

    sudo opkg install curl curl-dev 

erlang
----

    USER=root
    GUMSTIX=192.168.1.10

    cd ~/overo-oe
    bitbake erlang
    scp ./tmp/deploy/glibc/ipk/armv7a/erl*_armv7a.ipk ${USER}@${GUMSTIX}:~/

On the overo

    sudo opkg install ~/erl*

CouchDB
----

About 10 mins. Installs executable to `/usr/local`

    wget http://www.ecoficial.com/apachemirror//couchdb/1.0.1/apache-couchdb-1.0.1.tar.gz
    tar xf apache-couchdb-1.0.1.tar.gz
    cd apache-couchdb-1.0.1
    ./configure

Resources
====

  * [Mozilla.org: NSPR Build Instructions](https://developer.mozilla.org/en/NSPR_build_instructions)
  * [Mozilla.org: SpiderMonkey Build Documentation](https://developer.mozilla.org/en/SpiderMonkey_Build_Documentation)
    * [Mozilla.org: SpiderMonkey Linux Prerequisites](https://developer.mozilla.org/En/Developer_Guide/Build_Instructions/Linux_Prerequisites)
  * [SpiderMonkey on ARM (CROSS_COMPILE)](http://software.itags.org/mozilla/83286/)
  * [CouchDB on Android](http://github.com/apage43/couch-android-launcher/wiki/couchdb-build-notes-dump)

Appendix
====

`nspr` is no available through the `Angstrom` repository. I tried to bitbake it.

    USER=root
    GUMSTIX=192.168.1.10

    cd ~/overo-oe
    bitbake nspr
    scp ./tmp/deploy/glibc/ipk/armv7a/nspr_*-*_armv7a.ipk ${USER}@${GUMSTIX}:~/
    scp ./tmp/deploy/glibc/ipk/armv7a/nspr-static_*-*_armv7a.ipk ${USER}@${GUMSTIX}:~/

    sudo opkg install ~/nspr_*.ipk
    
Errors
----

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
