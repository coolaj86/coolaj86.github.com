--- 
layout: article
name: json-c-example
title: JSON-C Example
categories: thesystemisntdown uncategorized
time: 2010-06-28 10:17:00 -06:00
---
So you want to parse JSON with C? Welcome aboard!
First get json-c, configure, compile, and update your ld cache

Compile JSON-C
====

x86 / Ubuntu
----

    wget http://oss.metaparadigm.com/json-c/json-c-0.9.tar.gz
    tar xf json-c-0.9.tar.gz
    cd json-c-0.9
    ./configure && make && sudo make install
    sudo ldconfig

ARM / OpenEmbedded
----

    bitbake json-c

Simple Test
====

Now use one of the test files provided or this example:
http://www.jroller.com/RickHigh/entry/json_parsing_with_c_simple

`json-example.c`:
----

    #include &lt;stdio.h>
    #include &lt;stdlib.h>
    #include &lt;stddef.h>
    #include &lt;string.h>
    #include "json.h"
    int main(int argc, char **argv)
    {
      struct json_object *new_obj;
      MC_SET_DEBUG(1);
      // I added some new lines... not in real program
      new_obj = json_tokener_parse("/* more difficult test case */ { \"glossary\": { \"title\": \"example glossary\", \"pageCount\": 100, \"GlossDiv\": { \"title\": \"S\", \"GlossList\": [ { \"ID\": \"SGML\", \"SortAs\": \"SGML\", \"GlossTerm\": \"Standard Generalized Markup Language\", \"Acronym\": \"SGML\", \"Abbrev\": \"ISO 8879:1986\", \"GlossDef\": \"A meta-markup language, used to create markup languages such as DocBook.\", \"GlossSeeAlso\": [\"GML\", \"XML\", \"markup\"] } ] } } }");
      printf("new_obj.to_string()=%s\n", json_object_to_json_string(new_obj));
      new_obj = json_object_object_get(new_obj, "glossary");
      printf("new_obj.to_string()=%s\n", json_object_to_json_string(new_obj));
      new_obj = json_object_object_get(new_obj, "pageCount");
      int pageCount = json_object_get_int(new_obj);
      printf("Page count = %d", pageCount);
      json_object_put(new_obj);
      return 0;
    }

`Makefile`:
----

    all: static
    static:
      gcc -static json-example.c -L/usr/local/lib -ljson -I/usr/local/include/json -o json-example-static
      ./json-example-static
    shared:
      gcc json-example.c -L/usr/local/lib -ljson -I/usr/local/include/json -o json-example-shared
      ./json-example-shared
    .PHONY: all static shared

Make `libjson`
----

    make static
    make shared

Hopefully that's enough to get you started. The big thing is that when you compile your own projects, make sure that you include the json library and paths.

<img width='1' height='1' src='https://blogger.googleusercontent.com/tracker/3223021047265210707-429295978562103288?l=thesystemisntdown.blogspot.com' alt='' />
