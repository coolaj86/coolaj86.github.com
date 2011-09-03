---
layout: article
uuid: 6024CC70-3691-4757-813D-B3A2D843B139
title: Ubuntu 10.10 Maverick Meerkat on Gumstix Overo
name: ubuntu-10-10-maverick-meerkat-on-gumstix-overo
created_at: 2011-02-08
updated_at: 2011-02-08
categories: unfinished
---

On your `Host` Ubuntu system:

    sudo apt-get install rootstock
    sudo rootstock --help
    
    sudo rootstock \
      --dist "lucid" \
      --serial ttyS2 \
      --fqdn "gumstix" \
      --login "my_user" \
      --password "my_secret" \
      --fullname "My_Firstname My_Lastname" \
      --seed openssh-server,vim \
      --timezone "America/Denver" \
      --locale en_US.UTF-8 \
      --imagesize 2G \
      --components  main,universe,restricted,multiverse \
      --kblayout us


The output will end with something like this (ignore the segfault):

    I: Virtual Machine done
    I: Creating tarball from rootfs
    I: Mounting temporary Image
    /usr/bin/rootstock: line 195:  2019 Segmentation fault      qemu-system-arm $QEMUOPTS -append "${APPEND}" > $QEMUFIFO 2>&1
    I: ARM rootfs created as /mnt/local/home/coolaj86/armel-rootfs-201102081341.tgz
    I: Cleaning up...
    .....
    I: A logfile was saved as /mnt/local/home/coolaj86/rootstock-201102081341.log
    I: done ...


