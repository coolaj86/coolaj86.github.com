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

I recently bought a minimal installation of Ubuntu 10.04 on ThrustVPS.

It was a bit more minimal than I expected, but I think they've updated to be more reasonable since.
I had a friend just purchase his own and his system was much more useable than mine.

Update: Updated for 11.04 instructions

Locale
===

    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8

    dpkg-reconfigure locales

Absolute Essentials
===

    /etc/
    deb http://us.archive.ubuntu.com/ubuntu/ natty main restricted universe multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ natty-updates main restricted universe multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ natty-security main restricted universe multiverse


    apt-get update

    sudo apt-get install -y \
      ssh \
      rsync \
      fail2ban \
      vim

user accounts & security
====

    USER=myuser

    adduser ${USER}
    adduser ${USER} sudo

    exit

login as user and disallow root login

    VPS=myvps.com

    rsync -avh ~/.ssh/ ${USER}@${VPS}:~/.ssh/
    rsync -avh ~/.vimrc ${USER}@${VPS}:~/.vimrc
    rsync -avh ~/.gitconfig ${USER}@${VPS}:~/.gitconfig
    ssh ${USER}@${VPS}

    sudo vim /etc/ssh/sshd_config
    #ssh_config: PermitRootLogin no
    sudo service ssh restart

default editor

    update-alternatives --config editor
    3

hostname

    sudo hostname sub.domain.tld
    sudo bash -c "echo sub.domain.tld > /etc/hostname"


Bare Bones
====


    apt-get install -y \
      bash-completion \
      command-not-found \
      man-db \
      psmisc \
      tasksel

    #  dialog \

    sudo tasksel
    # Basic Server
    # SSH Server

    # remove other stuff - apache5, mysql, samba, postfix, sendmail, etc

`/etc/skel/.bashrc`

    if [ -f /etc/bash_completion ]; then
        . /etc/bash_completion
    fi

basic utils

    apt-get install -y \
      htop \
      iftop \
      dnsutils \
      curl \
      wget \
      ssh \
      fail2ban \
      vim

development tools
====

    sudo dd if=/dev/zero of=/128mb.swap bs=1M count=128
    sudo mkswap /128mb.swap
    sudo swapon /128mb.swap

    sudo apt-get install -y \
      git \
      build-essential \
      cmake \
      libssl-dev \
      gitosis \
      git-svn

    echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
    . ~/.bashrc
    mkdir ~/local

.bash_profile
---

    # Aliases
    alias ll='ls -lah'
    alias grep='grep --color=auto'

    # For user@host.domain.tld:/path/to/curdir shows user@host:curdir, colorized
    export CLICOLOR=1
    export PS1="\[\e[1;34m\]\u\[\e[0;37m\]@\[\e[0;32m\]\h\[\e[0;37m\]:\[\e[0;35m\]\W\[\e[0m\] \$ "

nodejs
----

    mkdir ~/Code
    cd ~/Code
    git clone git://github.com/joyent/node
    cd node
    git checkout v0.4.12
    rm -rf ~/Code/node/*
    git checkout v0.4.12 ./
    #./configure --prefix=/usr/local
    # make -j 4
    # make install
    make -f Makefile.cmake
    cd ~/Code/node/build
    sudo make install

    cd ~/
    curl http://npmjs.org/install.sh | sudo sh

    sudo npm install -g \
      validate-json \
      uglify-js \
      ender \
      spark

    # TODO package mkjs

    git clone git://github.com/coolaj86/connect-vhoster ~/webapps
    sudo mv ~/webapps /var/webapps
    ln -s /var/webapps ~/webapps
    cd ~/webapps
    cp config.default.js config.js
    mkdir -p vhosts
    npm install
    # /etc/init/webapps.conf must not be a symlink
    sudo cp ~/webapps/webapps.conf /etc/init/webapps.conf
    sudo -E /usr/local/bin/spark

TODO: show sample test webapp
