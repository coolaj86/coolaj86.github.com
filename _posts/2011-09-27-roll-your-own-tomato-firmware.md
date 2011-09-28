---
layout: article
uuid: 0B1A19EB-8142-4499-B551-37A8BEC9EBB1
title: Roll your own Tomato firmware
name: roll-your-own-tomato-firmware
created_at: 2011-09-27
updated_at: 2011-09-27
categories: thesystemisntdown
---

Goal
===

Customize and rebrand the Tomato firmware by modifying the Web Interface. 

Instructions
===

This has been tested from a fresh VirtualBox installs of Ubuntu 11.04 (both 32-bit and 64-bit tested) from OS X 10.7 Lion.

First you need to intsall the dependencies:

    sudo apt-get update
    sudo apt-get upgrade -y
    sudo apt-get install -y \
      virtualbox-ose-guest-utils \
      build-essential \
      zlib1g-dev \
      subversion \
      unrar \
      wget \
      curl \
      git \
      vim

Then you should checkout `firmware-mod-kit`:

    mkdir Code
    cd ~/Code
    svn checkout http://firmware-mod-kit.googlecode.com/svn/trunk/ firmware-mod-kit-read-only
    cd firmware-mod-kit-read-only/trunk

Next get your firmware build (currently build 54):

    # ASUS RT-N16 (32MB NAND, 128MiB RAM)
    curl -v 'http://downloads.sourceforge.net/project/tomatousb/Experimental%20%28beta%29/K26-MIPSR2/tomato-K26USB-1.28.9054MIPSR2-beta-Ext.rar?r=http%3A%2F%2Ftomatousb.org%2Fdownload&ts=1317161608&use_mirror=voxel' 2>&1 | grep Location
    wget 'http://voxel.dl.sourceforge.net/project/tomatousb/Experimental%20%28beta%29/K26-MIPSR2/tomato-K26USB-1.28.9054MIPSR2-beta-Ext.rar'

    # WRT54G-TM (8MB NAND, 32MiB RAM)
    curl -v 'http://downloads.sourceforge.net/project/tomatousb/Experimental%20%28beta%29/K26-MIPSR1/tomato-K26USB-1.28.9054MIPSR1-beta-Ext.rar?r=http%3A%2F%2Ftomatousb.org%2Fdownload&ts=1317164364&use_mirror=hivelocity' 2>&1 | grep Location
    wget 'http://hivelocity.dl.sourceforge.net/project/tomatousb/Experimental%20%28beta%29/K26-MIPSR1/tomato-K26USB-1.28.9054MIPSR1-beta-Ext.rar'

Extract:

    unrar x tomato-K26USB-1.28.9054MIPSR2-beta-Ext.rar
    ./extract_firmware.sh tomato-K26USB-1.28.9054MIPSR2-beta-Ext.trx ~/custom-tomato/
    # Or
    ./extract-ng.sh tomato-K26USB-1.28.9054MIPSR2-beta-Ext.trx ~/custom-tomato/

Modify:

    vim ~/custom-tomato/rootfs/www/basic-network.asp
    # change "(beta)" to "I AM THE WALRUS GOO GOO GACHU"

Repackage and Upgrade:

    ./build_firmware.sh ~/custom-tomato-rebuilds/ ~/custom-tomato/
    # or
    ./build-ng.sh ~/custom-tomato-rebuilds/ ~/custom-tomato/

Assuming that you already have default Tomato on your router
(which you definitely should before testing a custom build, duh!):

  0. As a precaution, do both a **Soft Reset** and **Hard Reset** of your router.
  0. Visit <http://192.168.1.1/admin-upgrade.asp>
    * if someone would hit me back with how to do this on curl, I'd appreciate it.
  0. As a precaution, select "After flashing, erase all data in NVRAM memory"
  0. Load `custom-tomato-rebuilds/custom_image_00001-asus.trx`

NOT FINISHED YET - Sept 28
