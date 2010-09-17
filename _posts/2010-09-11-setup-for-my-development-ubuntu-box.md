---
layout: article
title: setup for my development ubuntu box
categories: ofnousetoanyoneelse
updated_at: 2010-09-11
---

This is how I used to setup my server after a clean install... back in `08 or something...

    #!/bin/bash
    USER=`whoami`
    if [ $USER != 'root' ]
        then
            echo "You aren't root."
            exit 1
    fi

    APT_OPTIONS='-qq' #implies --yes --quiet
    WGET_OPTIONS='--no-verbose --quiet --output-document /tmp/server-ubuntu.log'

    echo "Minor house-keeping before starting..."
    apt-get $APT_OPTIONS autoremove
    apt-get $APT_OPTIONS autoclean
    clear


    ## BASICS ##
    tasksel install server
    tasksel install lamp-server
    tasksel install openssh-server

    ## PHP ##
    apt-get install php5-cli

    ## RAILS ##

    # Install prefork compatible passenger to work alongside php
    sh -c 'echo "deb http://apt.brightbox.net hardy main" > /etc/apt/sources.list.d/brightbox.list'
    sh -c 'wget -q -O - http://apt.brightbox.net/release.asc | apt-key add -'
    apt-get update
    apt-get install libapache2-mod-passenger rails libwww-mechanize-ruby
    apt-get $APT_OPTIONS install \
       rubygems \
       ruby-dev \
       libxslt-dev \
       libopenssl-ruby \
       libpdf-writer-ruby \
       libtransaction-simple-ruby \
       libactivesupport-ruby
    gem install mechanize hoe
    gem install fastthread
    gem update

    # OR install PHP through fastcgi
    #sudo apt-get install apache2-mpm-worker libapache2-mod-fcgid php5-cgi
