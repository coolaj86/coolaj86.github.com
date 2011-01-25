---
layout: article
uuid: 5905FA26-8033-4077-B677-8AF5269DF688
title: Updating a bitbake package
name: updating-a-bitbake-package
created_at: 2011-01-25
updated_at: 2011-01-25
categories: fastr
---

Goal
====

Update a bitbake file from a previous version to a newer version.

This is mainly just to help me remember. If you'd like clarification as to what I'm doing here, please comment.

Example: Node.js
====

Currently the Node.js available through OpenEmbedded is v0.2.1. Today I'll be updating it to v0.2.6.

Update to the latest current version from OpenEmbedded:

    cd ~/overo-oe/org.openembedded.dev/recipes/nodejs
    git fetch
    git checkout org.openembedded.dev ./

Move the file the most recent version. Then update the source and md5sums:

    git mv nodejs_0.2.1.bb nodejs_0.2.6.bb
    cat nodejs_0.2.6.bb | grep 'http'
    PV=0.2.6
    wget http://nodejs.org/dist/node-v${PV}.tar.gz
    md5sum node-v${PV}.tar.gz
    sha256sum node-v${PV}.tar.gz
    sed -i "s/\(md5sum.* = \)\".*\"/\1\"`md5sum node-v${PV}.tar.gz | cut -d' ' -f1`\"/" nodejs_${PV}.bb
    sed -i "s/\(sha256sum.* = \)\".*\"/\1\"`sha256sum node-v${PV}.tar.gz | cut -d' ' -f1`\"/" nodejs_${PV}.bb

Recreate patches:

    bitbake nodejs # fails, of course

    cd ~/overo-oe/tmp/work/armv7a-angstrom-linux-gnueabi/nodejs-0.2.6-r1/

    cp node-v0.2.6/deps/libev/wscript node-v0.2.6/deps/libev/wscript.orig
    vim node-v0.2.6/deps/libev/wscript # set execution of time code to false
    git diff --no-prefix \
      node-v0.2.6/deps/libev/wscript.orig node-v0.2.6/deps/libev/wscript \
      > ~/overo-oe/org.openembedded.dev/recipes/nodejs/files/libev-cross-cc.patch

    cp node-v0.2.6/wscript node-v0.2.6/wscript.orig
    vim node-v0.2.6/wscript # allow arm, mips, x86, x64 cross compiling
    git diff --no-prefix \
      node-v0.2.6/wscript.orig node-v0.2.6/wscript \
      > ~/overo-oe/org.openembedded.dev/recipes/nodejs/files/node-cross-cc.patch

    bitbake nodejs # now it works again

Submit to OE:

See guidelines at http://www.openembedded.org/index.php/Getting_started

Create a new branch with this feature

    git branch coolaj86
    git checkout coolaj86
    git add files/*.patch
    git add nodejs_*.bb
    git commit # leave a descriptive message

Now checkout the mainline oe branch

    # PreReqs
    # git clone git://git.openembedded.org/openembedded org.openembedded.dev
    # git remote rename origin openembedded
    # or
    # git clone git://gitorious.org/gumstix-oe/mainline.git org.openembedded.dev
    # git remote rename origin gumstix
    # git remote add openembedded git://git.openembedded.org/openembedded

    git checkout -b org.openembedded.dev.mainline openembedded/org.openembedded.dev
    git checkout coolaj86 recipes/nodejs/
    git commit # use same message as above
    git format-patch --subject-prefix="PATCH" -1
    git send-email \
      --to=openembedded-devel@lists.openembedded.org \
      0001-nodejs-updated-recipe-to-v0.2.6.patch

