---
layout: article
title: up and running with turbogears 2
categories: old
updated_at: 2010-09-12
---

Once upon a time I was interested in using TurboGears2.
I learned some stuff about virtualenv along the way.
This is a script I found in my `~/`.

Ubuntu 8.04
====

    apt-get install python-virtualenv
    apt-get install libapache2-mod-wsgi
    cd /var/webapps/libpython
    virtualenv --no-site-packages wsgi-baseline-python
    source wsgi-baseline-python/bin/activate
    vim /etc/apache2/mods-enabled/*wsgi*
    # WSGIPythonHome /wherever/you/keep/shared/libraries/python-wsgi-baseline
    apt-get install python-setuptools
    #wget http://peak.telecommunity.com/dist/ez_setup.py
    #python ez_setup -U setuptools
    apt-get install python-dev
    apt-get install build-essential


    wget http://www.turbogears.org/2.0/downloads/current/tg2-bootstrap.py
    python tg2-bootstrap.py --no-site-packages tg2env

    easy_install -i http://www.turbogears.org/2.0/downloads/current/index tg.devtools
