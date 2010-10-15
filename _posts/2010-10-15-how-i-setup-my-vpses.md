---
layout: article
uuid: 
title: How I setup my VPSes
name: how-i-setup-my-vpses
created_at: 2010-10-15
updated_at: 2010-10-15
categories: thesystemisntdown
---
ThrustVPS
====

I recently bought a minimal installation of Ubunut 10.04 on ThrustVPS.

It was a bit more minimal than I expected, but I think they've updated to be more reasonable since.
I had a friend just purchase his own and his system was much more useable than mine.

Bare Bones
====

    apt-get install -y \
      dialog \
      tasksel

    sudo tasksel
    # Basic Server
    # SSH Server

basic utils

    apt-get install -y \
      bash-completion \
      command-not-found \
      htop \
      curl \
      man-db \
      psmisc \
      wget \
      vim \

`.bashrc`

    if [ -f /etc/bash_completion ]; then
        . /etc/bash_completion
    fi

default editor

    update-alternatives --config editor
    3

user accounts & security
====

    USER=myuser
    adduser ${USER}
    adduser ${USER} sudo

    vim /etc/ssh/sshd_config
    ssh_config PermitRootLogin no
    /etc/init.d/ssh restart

    sudo apt-get install fail2ban
    deb http://us.archive.ubuntu.com/ubuntu/ lucid main restricted universe multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ lucid-updates main restricted universe multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ lucid-security main restricted universe multiverse

development tools
====

    apt-get install -y \
      git-core \
      gitosis \
      build-essential \
      libssl-dev \
      git-svn

    echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
    . ~/.bashrc
    mkdir ~/local

nodejs
----

    git clone http://github.com/ry/node.git
    cd node
    git checkout v0.2.3
    ./configure --prefix=~/local
    make -j 4 # builds in 11seconds.... WOW!
    make install

    curl http://npmjs.org/install.sh | sh

    npm install connect express spark futures

    mkdir webapps

TODO: show sample test webapp

    sudo -E ~/local/bin/spark
    /etc/init/node-vhosts.conf # cannot be symlink
