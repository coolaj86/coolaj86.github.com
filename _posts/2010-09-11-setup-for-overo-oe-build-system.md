---
layout: article
uuid: e31e9967-d082-45e5-bb50-70104f708fea
title: setup for overo oe build system
categories: gumstix
updated_at: 2010-09-11
---

The short version of the official doc.

Rumor has it that builds work best on the latest version of ubuntu (10.04 right now).
The maintainer upgrades regularly and some native packages mismatch on older Ubuntu versions.

    #!/bin/bash
    #http://www.gumstix.net/Setup-and-Programming/view/Overo-Setup-and-Programming/Setting-up-a-build-environment/111.html

    set -e 

    mkdir -p ~/overo-oe

    cd ~/overo-oe
    git clone git://gitorious.org/gumstix-oe/mainline.git org.openembedded.dev
    cd org.openembedded.dev
    git checkout --track -b overo origin/overo

    cd ~/overo-oe
    git clone git://git.openembedded.net/bitbake bitbake
    cd bitbake
    git checkout 1.8.18

    cd ~/overo-oe
    cp -r org.openembedded.dev/contrib/gumstix/build .

    cp ~/.bashrc ~/bashrc.bak.pre-oe
    cat ~/overo-oe/build/profile >> ~/.bashrc
    source ~/overo-oe/build/profile

    sudo rm /bin/sh
    sudo ln -s /bin/bash /bin/sh
    sudo apt-get install help2man diffstat texi2html texinfo cvs gawk chrpath
    sudo apt-get install python-psyco

    bitbake omap3-console-image
