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

I like <http://thrustvps.com>. They've been good to me and reliable. :-D.

Locale
===

I find that if I don't manually set the locale for my VPSes, it won't be set and then I get a bunch of `perl` errors.

    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8

    dpkg-reconfigure locales

Absolute Essentials
===

I've had at least one case where the repositories had been left empty, so it's always good to check:

    cat universe /etc/apt/sources.list
    # you should see these two sources listed in the file
    #deb http://us.archive.ubuntu.com/ubuntu/ lucid main universe multiverse
    #deb http://us.archive.ubuntu.com/ubuntu/ lucid-security main universe multiverse
    # you may or may not want to add regular updates in addition to security updates
    #deb http://us.archive.ubuntu.com/ubuntu/ lucid-updates main restricted universe multiverse

    sudo apt-get update

    sudo apt-get install -y \
      ssh \
      rsync \
      fail2ban \
      vim

user accounts & security
====

Replace `myuser` with the name of the user you wish to create.

You will be prompted to enter user profile details. You can just leave everything blank, but I like to put my name.

    USER=myuser
    adduser ${USER}

After you put in your name you can just leave the rest blank.

    adduser ${USER} sudo
    exit # leave the VPS and get back to your own system

Now back on your own computer test the account and copy over your favorite user preferences.

Change `myuser` and `myvps.com` to your user name and your domain / website name (or your IP address if you haven't managed your DNS yet).

    USER=myuser
    VPS=myvps.com

    rsync -avh ~/.ssh/ ${USER}@${VPS}:~/.ssh/
    rsync -avh ~/.vimrc ${USER}@${VPS}:~/.vimrc
    rsync -avh ~/.gitconfig ${USER}@${VPS}:~/.gitconfig

You should never login as root. Now that your user is set up and tested working, root should be disabled right away.

    ssh ${USER}@${VPS}

    sudo vim /etc/ssh/sshd_config

Look for the line with `PermitRootLogin yes` and change it to `PermitRootLogin no`. WARNING: never do this from the root account. Always do it from your user account.

    sudo service ssh restart

Change `myvps.com` to your hostname / domain / website (even if you haven't configured it yet in DNS)

    VPS=myvps.com
    sudo hostname ${VPS}
    sudo bash -c "echo ${VPS} > /etc/hostname"


Now change your default editor to your editor of choice (which should be vim).

    update-alternatives --config editor

Bare Bones
====

    sudo apt-get install -y \
      bash-completion \
      command-not-found \
      man-db \
      psmisc \
      tasksel

    #  dialog \

    sudo tasksel
    # Basic Server - 1
    # SSH Server - 12

    # remove other stuff - apache5, mysql, samba, postfix, sendmail, etc

`/etc/skel/.bashrc`

    if [ -f /etc/bash_completion ]; then
        . /etc/bash_completion
    fi

basic utils

    sudo apt-get install -y \
      htop \
      iftop \
      dnsutils \
      ntpdate \
      curl \
      wget \
      ssh \
      fail2ban \
      vim

    sudo ntpdate ntp.ubuntu.com
    # note, some VPSes don't allow setting the time

development tools
====

    sudo dd if=/dev/zero of=/128mb.swap bs=1M count=128
    sudo mkswap /128mb.swap
    sudo swapon /128mb.swap
    # node, some VPSes don't allow swap

    sudo apt-get install -y \
      git-core \
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

Load webapps into `~/webapps/vhosts/`, test that they work, and start the webapps service.

    sudo -E /usr/local/bin/spark
    sudo service webapps start

TODO: show sample test webapp

TODO: github hooks
