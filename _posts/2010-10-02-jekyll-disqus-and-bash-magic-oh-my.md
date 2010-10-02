---
layout: article
uuid: d2322705-d790-49e5-8ed3-753d5c1cbb16
title: Jekyll, DISQUS, and Bash magic. Oh my!
categories: thesystemisntdown
updated_at: 2010-10-02
---

I have a script `mkblog` which

  * Creates a slugged blog document
  * Links the document without the date (for easy editing)
  * Generates a **uuid** (for use as `disqus_identifier`)

Here's a snippet:

    cat - > /tmp/${FILE} << EOF
    ---
    layout: article
    uuid: `uuidgen`
    title: `echo ${TITLE}`
    created_at: `date +'%Y-%m-%d'`
    updated_at: `date +'%Y-%m-%d'`
    categories: unfinished
    ---
    EOF

In my `_layouts/default.html`, right above the closing `</body>`,
I have the disqus-provided JavaScript.

    <script type="text/javascript">
    var disqus_shortname = 'coolaj86-gh';
    (function () {
      var s = document.createElement('script'); s.async = true;
      s.src = 'http://disqus.com/forums/coolaj86-gh/count.js';
      (document.getElementsByTagName('HEAD')[0] || document.getElementsByTagName('BODY')[0]).appendChild(s);
    }());
    </script>
    </body> <!-- RIGHT ABOVE THE END -->

In my `_layouts/post.html` I put a little conditional logic to load Disqus comments for all of the posts I've made since I started including uuids.

    {% if page.uuid %}
    <div id="disqus_thread"></div>
    <script type="text/javascript">
        var disqus_identifier = disqus_identifier && 'badid' || '{{ page.uuid }}';
      (function() {
       var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
       dsq.src = 'http://coolaj86-gh.disqus.com/embed.js';
       (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
      })();
    </script>
    <noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript=coolaj86-gh">comments powered by Disqus.</a></noscript>
    <a href="http://disqus.com" class="dsq-brlink">blog comments powered by <span class="logo-disqus">Disqus</span></a>
    {% endif %}


Appendix
====

Add UUID's to Existing posts
----

    ls _posts/ | while read P; do sed -i _posts/${P} -e "s|\(^title:.*\)|uuid: `uuidgen`\n\1|g"; done

Create short-name links
----

    ls _posts/ | while read N; do NEW=`echo $N | cut -d'-' -f4-99`; echo "ln -s ../_posts/$N ./edit/$NEW"; done


./mkblog
----

    #!/bin/bash
    set -e
    EDITOR=vim

    TITLE=${1}
    POSTDIR=_posts
    EDITDIR=edit

    if [ ! -n "${TITLE}" ] || [ -n "${2}" ]
    then
      echo "USAGE: ${0} 'Title of my Blog Super-Post!'"
      exit 1
    fi

    SLUG=`echo "${TITLE}" \
      | tr '[A-Z]' '[a-z]' \
      | tr ' ' '-' \
      | sed 's/[^a-zA-Z0-9\-]/-/g' \
      | sed 's/-\+/-/g' \
      | sed -e 's/^-//g' -e 's/-$//g'`

    FILE=`date '+%Y-%m-%d'`-${SLUG}.md
    EDFILE=${EDITDIR}/${SLUG}.md

    if [ ! -e ${POSTDIR}/${FILE} ]
    then
      mkdir -p "${POSTDIR}"
      cat - > /tmp/${FILE} << EOF
    ---
    layout: article
    title: `echo ${TITLE}`
    categories: unfinished
    updated_at: `date +'%Y-%m-%d'`
    uuid: `uuidgen`
    ---

      HEY YOU: Remember to changed the category from unfinished.
    EOF

      cat /tmp/${FILE}
      mv /tmp/${FILE} ${POSTDIR}/${FILE}

      mkdir -p ${EDITDIR}
      ln -s ../${POSTDIR}/${FILE} ${EDFILE}
    fi

    $EDITOR ${EDFILE}
    echo "Saved ${EDFILE}."
