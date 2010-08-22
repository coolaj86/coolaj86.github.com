---
layout: article
title: Migrate from blogger to jekyll
categories: meta
updated_at: 2010-08-21
---
Goal
====

Migrate from blogspot to Jekyll.

Pre-reqs
=====

Turn full rss feeds on for your blog

  0. Navigate to your blog `http://yourblog.blogspot.com`
  0. `Sign in`
  0. Click on the `edit` link for any post
  0. Click `settings`
  0. Click `site feed`
  0. Click `advanced mode`
  0. Select `full` for all options
  0. Save

Import with `vilcans`' Jekyll `rss_importer`
=====

    BLOGGER=coolaj86

    git clone http://github.com/vilcans/jekyll.git
    cd jekyll/
    git branch -a
    git checkout origin/rss_importer
    git checkout -b rss_importer 
    git branch
    mkdir -p _posts
    sed -i "s/require \"YAML\"/require \"yaml\"/" ./lib/jekyll/converters/rss.rb
    wget http://${BLOGGER}.blogspot.com/feeds/posts/default?alt=rss -O ${BLOGGER}.rss.xml
    ruby -r './lib/jekyll/converters/rss' -e 'Jekyll::RSS.process("'${BLOGGER}'.rss.xml")'

Possible Errors
=====

If you didn't enable full rss feeds (and click save):

    No content in RSS item '2006_03_01_archive'
    Created 0 posts!

If you didn't replace "YAML" with "yaml":

    /home/user/jekyll/lib/jekyll/converters/rss.rb:5:in `require': no such file to load -- YAML (LoadError)
      from /home/user/jekyll/lib/jekyll/converters/rss.rb:5:in `<module:Jekyll>'
      from /home/user/jekyll/lib/jekyll/converters/rss.rb:1:in `<top (required)>'
      from ruby:0:in `require'

If you don't have a `_posts`:

     http://coolaj86.blogspot.com/2010_05_01_archive.html#8976446356395410673 -> _posts/2010-05-06-2010_05_01_archive.html
    /home/user/jekyll/lib/jekyll/converters/rss.rb:39:in `initialize': No such file or directory - _posts/2010-05-06-2010_05_01_archive.html (Errno::ENOENT)
      from /home/user/jekyll/lib/jekyll/converters/rss.rb:39:in `open'
      from /home/user/jekyll/lib/jekyll/converters/rss.rb:39:in `block in process'
      from /usr/local/lib/ruby/1.9.1/rexml/element.rb:906:in `block in each'
      from /usr/local/lib/ruby/1.9.1/rexml/xpath.rb:64:in `each'
      from /usr/local/lib/ruby/1.9.1/rexml/xpath.rb:64:in `each'
      from /usr/local/lib/ruby/1.9.1/rexml/element.rb:906:in `each'
      from /home/user/jekyll/lib/jekyll/converters/rss.rb:16:in `process'
      from -e:1:in `<main>' 
