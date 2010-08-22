---
layout: article
title: Migrate from blogger to jekyll
categories: meta
updated_at: 2010-08-21
---
Goal
====

Migrate from blogspot to Jekyll.

todo
----

  * indexing related posts with --lsi
  * parse formatted content (not likely to be accomplished)

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

Import
=====

The caveats are that you either lose a lot of formatting or a lot of your time. You pick.

with `vilcans`' Jekyll `rss_importer`
-----

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

Use the by-hand approach
------

    BLOGGER=coolaj86

    wget --convert-links --html-extension --mirror --random-wait --wait 3 http://${BLOGGER}.blogspot.com/

Essentially you would want to parse 

  * `YYYY_MM_DD_title.html` as the filename and `name: `
  * `<title>` up to `</title>` and put it in `title: `
  * `<div class='post-body entry-content'>` until `<div class='post-footer'>` and put it after the YAML front-matter.
  * `<h2 class='date-header'>` up to `</h2>` and put it in as `date: ` (includes time, which the filename doesn't)

If you write a script to strip out all of the garbage and keep the post + formatting, I'd love to hear about it.

Here's a post that will get you halfway to [converting html to markdown][html2md].

[html2md]: http://whathesaid.ca/2008/02/11/how-to-convert-a-websites-content-into-simple-text-files/

Categorise by Blog
-----

I'm using [`Fastr`][fastr] as a template for my blog. `Fastr` supports categories with vanilla `Jekyll`.

[fastr]: http://github.com/fastr/fastr.github.com

Here's a script I used to go through one of my blogs, which was created back when there was no `title` field:

    BLOG=coolaj86
    ID=0 # Fastr doesn't allow posts of the same name

    cd ${BLOG}_posts
    ls | while read POST; do
      sed -i "s/^title:/title: untitled ${ID}\ncategories: ${BLOG} uncategorized/" ${POST}
      mv `basename ${POST} .html` ${POST}_${ID}.md
      let ID=ID+1
    done

And the other, which thankfully did have titles:

    BLOG=thesystemisntdown

    cd ${BLOG}_posts
    ls | while read POST; do
      sed -i "s/^\(title:.*\)/\1\ncategories: ${BLOG} uncategorized/" ${POST}
      mv `basename ${POST} .html` ${POST}.md
      let ID=ID+1
    done


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
