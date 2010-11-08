---
layout: article
uuid: e31e9967-d082-45e5-bb50-70104f708fea
title: setup for overo oe build system
categories: gumstix-overo openembedded
updated_at: 2010-09-11
---

The short version of the official doc.

Rumor has it that builds work best on the latest version of ubuntu (10.04 right now).
The maintainer upgrades regularly and some native packages mismatch on older Ubuntu versions.

WARNING: You **must not have symbolic links** anywhere above or below the overo-oe directory.

If your `/home` is mounted on another drive such as `/mnt/local/home` you must create your overo-oe outside of your home directory.

    #!/bin/bash
    #http://www.gumstix.net/Setup-and-Programming/view/Overo-Setup-and-Programming/Setting-up-a-build-environment/111.html

    OVERO_OE=~/overo-oe

    set -e 

    mkdir -p ${OVERO_OE}

    cd ${OVERO_OE}
    git clone git://gitorious.org/gumstix-oe/mainline.git org.openembedded.dev
    cd org.openembedded.dev
    git checkout --track -b overo origin/overo

    cd ${OVERO_OE}
    git clone git://git.openembedded.net/bitbake bitbake
    cd bitbake
    git checkout 1.8.18

    cd ${OVERO_OE}
    cp -r org.openembedded.dev/contrib/gumstix/build .

    cp ~/.bashrc ~/bashrc.bak.pre-oe
    cat ${OVERO_OE}/build/profile >> ~/.bashrc
    source ~/.bashrc
    vim ~/.bashrc # replace ~/overo-oe with your path

    sudo rm /bin/sh
    sudo ln -s /bin/bash /bin/sh
    sudo apt-get install help2man diffstat texi2html texinfo cvs gawk chrpath
    sudo apt-get install python-psyco

    bitbake omap3-console-image
