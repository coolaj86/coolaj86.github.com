Jekyll-aid
====

Jekyll, the ruby-based blog-aware site builder has no default setup.

Jekyll-aid provides a nice "template" for a Jekyll-based site.

Usage
====

    ./mkdocument 'Title of my Awesome-blog post!!!'

  * creates `_posts/2010-10-14-title-of-my-awesome-blog-post.md`
  * create link `edit/title-of-my-awesome-blog-post.md`
  * adds `created_at` timestamp
  * creates `UUID` (for disqus comments)

Prepare
====

Accounts you'll need:

  * Google Analytics
  * Disqus Comments

Files to edit:

  * `all.html` -- change the `title`
  * `index.html` -- change the `title`
  * `_layouts/default.html` -- replace `___YOUR_ID_HERE___` and `___YOUR_UA_HERE` with your `disqus id` and `google analytics id`
  * `_layouts/article.html` -- replace `___YOUR_ID_HERE___` with your `disqus id`
  * `_config.yml` -- change the `destination`
  * `CNAME` -- if you're hosting on github and have a custom domain, enter it here (*without* the www prefix)

TODO
====

  * `./editdocument edit/title-of....`
    * updates `updated_at` date automagically

  * join forces with / steal ideas from Jekyll-base
    * [Jekyll, YUI, and Github...](http://www.nsa.be/index.php/eng/Blog/Jekyll-YUI-and-Github-a-great-combinatio)n
    * [Jekyll Base](http://raphinou.github.com/jekyll-base/)

  * Post about this to [the Jekyll mailing list](http://groups.google.com/group/jekyll-rb)
