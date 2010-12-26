---
layout: article
uuid: 6C1185A4-C317-4B97-AE83-9F6E747E101D
title: Lighttpd (supersedes Apache) and PHP on Gumstix
name: lighttpd-supersedes-apache-and-php-on-gumstix
created_at: 2010-12-26
updated_at: 2010-12-26
categories: gumstix
---

If you haven't been paying any attention to web development in the past 5 years or you're stuck with a legecy software stack, you probably got here by Googling for "Apache PHP Gumstix".

I would certainly advise you to use a programming language such as [`javascript`](http://nodejs.org), `python`, `ruby`, [`go`](http://golang.org), `erlang`, or any number of others which are suitable for web use to program. I would also certainly advise you to not use a confused template system run amok (that being PHP). However, I understand that sometimes... ugh... choices are limited or ignorance compels you to go back to the bad habits you're used to...

I'd also advise you to use a web server capable of handling request efficiently such as `nginx`, [`Node.JS`](http://nodejs.org), `lighttpd`, or any of a number of other event-driven web servers. I also advise you to not ues an outdated thread-spawning resource hog (that being Apache).

In this tutorial I assume that you have no intention of programming, just templating, so we'll use PHP, against my better judgement (yes, I really really really hate PHP). Since lighttpd does have modules for php and is easy to set up, we'll use that.

Superseding Apache with Lighttpd
====

You'll notice that there isn't any package for apache2-mod-php (or any other apache modules for that matter) in the Angstrom repository. However, there are plenty of modules for lighttpd. I choose the no-headache option.

    opkg list | grep httpd
    opkg install lighttpd
    opkg install lighttpd-module-fastcgi

Configuring lighttpd for static pages
====

    mkdir -p /srv/www/
    vim /etc/lighttpd.conf

You may with to change the document root (note the trailing slash):

    server.document-root        = "/srv/www/"

And perhaps the user (if you have created one other than root)

    server.username            = "root"
    server.groupname           = "root"


Configuring lighttpd with PHP
====

Just install the `php-cgi` package

    opkg list | grep php
    opkg install php php-cgi php-cli php-pear
    vim /etc/lighttpd.conf

Just unocmment to enable `mod_fastcgi`

    server.modules              = (
    # snip snip
                                    "mod_fastcgi",
    # snip snip

                                  )

And uncomment the PHP server configuration.

    #### fastcgi module
    ## read fastcgi.txt for more info
    ## for PHP don't forget to set cgi.fix_pathinfo = 1 in the php.ini
    fastcgi.server             = ( ".php" =>
                                   ( "localhost" =>
                                     (
                                       "socket" => "/tmp/php-fastcgi.socket",
                                       # Default was "bin-path" => "/usr/local/bin/php"
                                       # Gumstix proper:
                                       "bin-path" => "/usr/bin/php-cgi"
                                     )
                                   )
                                )

Note the change of the default `bin-path` to `/usr/bin/php-cgi`

Test the configuration
===

Create a test page:

    cat - > /srv/www/index.php << EOF
    <?php
      phpinfo();
    ?>
    EOF

Restart `lighttpd`

    /etc/init.d/lighttpd restart

Congratulate yourself
===

Congratulate yourself. Give yourself a slap on the face... er, back...

You are now perpetuating one of the most wretched problems with the web and web-based devices.

What are you going to do next? Install Windows? Rock on with your (literally) bad self.

... I hope you feel dirty.
