---
layout: article
uuid: 82bf66ff-509a-4a6a-9041-5bae0860528e
title: Eagle PCB Software on Ubuntu 11.04 x86_64 amd64
name: eagle-pcb-software-on-ubuntu-11-04-x86-64-amd64
created_at: 2011-09-14
updated_at: 2011-09-14
categories: thesystemisntdown
---

2011... Linux has had 64-bit support for what?
15+ years now?
And yet you still can't set up a 64-bit OS without quirks.

Anyway, for those of you that speak [English](http://translate.google.com/translate?sl=auto&tl=en&js=n&prev=_t&hl=en&ie=UTF-8&layout=2&eotf=1&u=http%3A%2F%2Fwww.mikrocontroller.net%2Ftopic%2F147596) better than [German](http://www.mikrocontroller.net/topic/147596).

    wget ftp://ftp.cadsoft.de/eagle/program/5.11/eagle-lin-5.11.0.run

    sudo apt-get install \
      lib32asound2 \
      lib32gcc1 \
      lib32ncurses5 \
      lib32stdc++6 \
      lib32z1 \
      libc6-i386 \
      ia32-libs 

    sh ./eagle-lin-5.11.0.run

The error you get when you attempt to install Eagle without installing the ia32 x86 libs are akin to these:

    eagle-lin-5.6.0.run: 107: /tmp/eagle-setup.0123/eagle-5.6.0/bin/eagle: not found
    eagle-lin-5.7.0.run: 107: /tmp/eagle-setup.1234/eagle-5.7.0/bin/eagle: not found
    eagle-lin-5.8.0.run: 107: /tmp/eagle-setup.2345/eagle-5.8.0/bin/eagle: not found
    eagle-lin-5.9.0.run: 107: /tmp/eagle-setup.3456/eagle-5.9.0/bin/eagle: not found
    eagle-lin-5.10.0.run: 107: /tmp/eagle-setup.4567/eagle-5.10.0/bin/eagle: not found
    eagle-lin-5.11.0.run: 107: /tmp/eagle-setup.5678/eagle-5.11.0/bin/eagle: not found
