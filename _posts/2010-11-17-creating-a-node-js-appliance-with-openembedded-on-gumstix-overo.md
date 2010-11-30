---
layout: article
uuid: 9B6907FD-0644-410E-9274-10AEDCF5ED35
title: Creating a Node.JS appliance with OpenEmbedded on Gumstix Overo
name: creating-a-node-js-appliance-with-openembedded-on-gumstix-overo
created_at: 2010-11-17
updated_at: 2010-11-17
categories: unfinished
---

Goal
====

Create a bitbake image with `Node.JS`, `upstart`, and other tools that might be useful for a multi-media web-based appliance.

Pre-Reqs
=====

This process has been tested on an Ubuntu 10.04 LTS development server.

You should have read

  * [Partitioning microSDHC for Gumstix Overo](http://fastr.github.com/articles/Partition-MicroSDHC-for-Gumstix-Overo.html)
  * [Formatting microSDHC for Gumstix Overo](http://fastr.github.com/articles/Format-MicroSDHC-for-Gumstix-Overo.html)
  * [NAND-less u-boot configuration and scripting](http://fastr.github.com/articles/booting-the-overo-tide.html)

Setting up the Build Environment
====

If you would like explanation as well, please see the official
[Gumstix Documentation](http://www.gumstix.net/Setup-and-Programming/view/Overo-Setup-and-Programming/Setting-up-a-build-environment/111.html)
.

    sudo dpkg-reconfigure dash # select no, use bash instead of dash
    # OR
    # cd /bin
    # sudo rm sh
    # sudo ln -s bash sh

    sudo bash -c 'echo "vm.mmap_min_addr = 0" > /etc/sysctl.d/mmap_min_addr.conf'
    sudo bash -c 'echo "vm.vdso_enabled = 0" > /etc/sysctl.d/vdso.conf'
    sudo sysctl -p

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

    cp ~/.bashrc ~/bashrc.bak
    cat ~/overo-oe/build/profile >> ~/.bashrc
    source ~/overo-oe/build/profile

**WARNING**: You must **not have symlinks** above or below the `overo-oe` directory

Server Configuration
====

    cd ~/overo-oe

    vim build/conf/site.conf
    # adjust according to the number of system cores (mine has 4)
    # PARALLEL_MAKE = "-j 4"
    # BB_NUMBER_THREADS = "4"

    vim build/conf/local.conf
    # choose the kernel image to use
    # PREFERRED_PROVIDER_virtual/kernel = "linux-omap-psp"
    # MACHINE_EXTRA_RRECOMMENDS += " dsplink-module ti-cmemk-module"


Node.JS Appliance Image
====

`upstart` should replace `sysvinit` on a Node.JS appliance.

    cd ~/overo-oe
    mkdir -p user.collection/recipes/images/

`nodejs-appliance-base-image.bb`:

The only part I understand about this is `IMAGE_INIT_MANAGER` is being
changed to `upstart` instead of `sysvinit`.

The rest is just copied from `task-base-image.bb`.

    #Angstrom bootstrap image

    PR = "r0"

    PREFERRED_PROVIDER_virtual/kernel = "linux-omap-psp"

    IMAGE_PREPROCESS_COMMAND = "create_etc_timestamp"

    ANGSTROM_EXTRA_INSTALL ?= ""

    IMAGE_INIT_MANAGER = "upstart upstart-sysvcompat sysvinit-utils"

    ZZAPSPLASH = ' ${@base_contains("MACHINE_FEATURES", "screen", "psplash-zap", "",d)}'

    DEPENDS = "task-base \
               ${SPLASH} \
               ${ZZAPSPLASH} \
         "

    IMAGE_INSTALL = "task-base \
          ${ANGSTROM_EXTRA_INSTALL} \
          ${SPLASH} \
          ${ZZAPSPLASH} \
          "

    IMAGE_LINGUAS = ""

    inherit image

`nodejs-appliance-image.bb`

I don't understand the different between `DEPENDS` and `IMAGE_INSTALL`,
but somehow this gets the things installed that I want.

    #NodeJS-Appliance bootstrap image
    require nodejs-appliance-base-image.bb

    PR = "r0"

    # libjpeg8 is just linked to somewhere by accident
    DEPENDS += "task-base-extended \
          htop \
          vim \
          grep \
          nodejs \
          start-stop-daemon \
          rsync \
          gzip \
          tar \
          ffmpeg \
          openssl \
          libpng \
          gpsd \
          openssh \
        "

    IMAGE_INSTALL += "task-base-extended \
          htop \
          vim \
          vim-syntax \
          grep \
          nodejs \
          start-stop-daemon \
          rsync \
          gzip \
          tar \
          ffmpeg \
          libpng \
          openssl \
          gpsd \
          openssh \
        "

    #      libstdc++6 \
    #      ntpdate \

    export IMAGE_BASENAME = "nodejs-appliance-image"

`nodejs-appliance-dev-image.bb`:

This image extends the image above, but includes more tools
for development.

Unfortunately, it appears that `task-sdk-native` requires `sysvinit`.

    # Node.JS Appliance develpment image
    require nodejs-appliance-image.bb

    PR = "r12"
    PREFERRED_PROVIDER_virtual/kernel = "linux-omap-psp"

    # task-sdk-native doesn't build at the moment
    # but must be included

    # python shouldn't be necessary once node switches from waf to cmake
    # that should come within the next few months

    # libopenssl-dev is needed for node
    # libstdc++-dev is only needed to build inotify
    DEPENDS += "\
        task-sdk-native \
        gdb \
        git \
        curl \
        python \
        "

    #    python-misc \

    IMAGE_INSTALL += "\
        task-sdk-native \
        gdb \
        git \
        curl \
        python \
        python-modules \
        openssh-scp \
        openssl-dev \
        "

    #    openssh-scp \
    #    openssl-dev \
    #    libstdc++6-dev \
    #    python \
    #    python-misc \
    #    python-modules \
    #    python-subprocess \

    export IMAGE_BASENAME = "nodejs-appliance-dev-image"

Installing the Image
====

    SDHC=/dev/sde

    cd ~/overo-oe
    bitbake x-loader
    bitbake u-boot-omap3
    bitbake nodejs-appliance-image.bb

    sudo mkdir /mnt/overo
    sudo mount /dev/${SDHC}2 /mnt/overo
    sudo mkdir /dev/${SDHC}1 /mnt/overo/boot
    sudo cp tmp/deploy/glibc/images/overo/MLO-overo /mnt/overo/boot/MLO
    sudo cp tmp/deploy/glibc/images/overo/u-boot-overo.bin /mnt/overo/boot/u-boot.bin
    sudo cp tmp/deploy/glibc/images/overo/uImage-overo.bin /mnt/overo/boot/uImage

    sudo tar xf tmp/deploy/glibc/images/overo/nodejs-appliance-image-overo.tar.bz2 -C /mnt/overo/

Preparing the Image
====

    rm /etc/rc*.d/**
    rm /etc/rc*.d/*usb-gadget*
    rm /etc/rc*.d/*dbus*
    rm /etc/rc*.d/*bluetooth*
    rm /etc/rc*.d/*avahi*

    cat - > /etc/resolv.conf.default << EOF
    nameserver 8.8.8.8
    nameserver 8.8.4.4
    EOF

    mkdir -p /usr/local/bin
    cat - > /usr/local/bin/networking-restart << EOF
    #!/bin/sh
    /etc/init.d/networking restart
    cp /etc/resolv.conf.default /etc/resolv.conf
    # Using FreeDNS as DynDNS
    wget http://freedns.afraid.org/dynamic/update.php?XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    EOF

    cd /etc/init.d
    ln -s ../../usr/local/bin/networking-restart ./
    cd ../rc2.d/
    ln -s ../init.d/networking-restart S98networking-restart

    /etc/init.d/networking-restart

    opkg update
