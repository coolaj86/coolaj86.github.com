---
layout: article
title: Self-organizing Media Database
categories: mediabox
updated_at: 2010-09-13
---
MediaBox
====

MediaBox is a plugin framework for organizing media, meta-data, and revisions such as the Music, Movies, Documents, etc on your computer.

This media database is part of a package which runs on a Gumstix Overo Fire, which is constrained.
Using event-oriented / lazy-evaluation architecture is important.

  * ARM Cortex A8 @ 500MHz (Overdrive mode 720MHz)
  * TI DSP @347MHz? (Overdrive mode 500MHz)

Examples of data handled:

  * file meta-data (mtime, uid, size, checksum)
  * media meta-data (artist, author, copyright, purchase date, account)
  * stream meta-data (bitrate, encoding, checksum)

Documentation
====
Documentation should be in a machine-readable format (preferably JSON), which can be used to create a web page.

Handlers
====

Each handler is a separate application. It is preferred that the handlers be written in NodeJS,
but Python is the best choice in many cases due to the availability of existing libraries that
very nearly meet the requirements as-is.

Input:

  * both filepath or data stream
  * parameter to request JSON
  * parameter to not calculate checksum
  * parameter to only caluculate checksum

Process:

  * Determines if this handler can process this file or not
  * Understands media meta-data (headers and footers containing information useful to humans)
  * Can interpret stream meta-data (checksum, bitrate, quality, etc)

Output:

  * Strings as Unicode / UTF-8 (protobuf handles this)
  * JSON string (dates as unix timestamp)
  * protobuf output
  * Native object (native dates)
  * Human readable format (dates something like "2010 Sept 14th" or ISO or European)
  * no excess heirarchy - keep things as top-level as reasonable

The output format should represent all meta-data where reasonable,
but at least as much as the common consumer applications
(iTunes, Picasa, Adobe Reader, etc) 

Example: IPTC defines a huge number of tags, but applications such as Picasa, Gthumb, and Flickr show the most relevant ones.

In cases where one format supports a feature that another doesn't, the output should handle the higher fidelity cases.

Example: One tag format allows only one Artist, another allows an array. An array should be used for the json output,
even if only one item is present.

The most import feature now is simply to read the various meta-data. 
The ability to add / remove / edit meta-data is a plus.

file
----

  * All files will match this handler
  * file meta-data from the filesystem
    * not stream meta-data
    * not media meta-data

audio
----

Python's mutagen provides most of the necessary functionality. AtomicParsley is also very helpful.

  * All common formats supported by all common containers
    * ID3, M4A/iTunes, any others? maybe fla?
    * MP3, AAC, OGG, FLAC support
  * M4A / iTunes support

Output should include bitrate if possible.

video
----

  * m4v / iTunes
  * avi
  * maybe flv?

images
----

  * Must be able to read EXIF, XML, and IPTC meta-data types.
    * [pyexiv2 (python)](http://tilloy.net/dev/pyexiv2/)
    * [libiptcdata (C)](http://libiptcdata.sourceforge.net/)
    * [thebigpicture (python)](http://code.google.com/p/thebigpicture/)
    * [Open Source Image Archiving: Exif, IPTC, XMP and all that](http://www.sitepoint.com/blogs/2006/08/24/open-source-image-archiving-exif-iptc-xmp-and-all-that/)

  * Must be able to extract and checksum the common JPEG, JFIF, etc data types - not just the strict "per-spec" files, but also those found "in-the-wild"
    * [StackOverflow: How to Checksum JPEG data (not the whole file)](http://stackoverflow.com/questions/1971567/checksum-jpeg-data-not-the-whole-file)

documents
----

**NOTE**: This is for future reference, not in the current plan

  * PDF - text (including OCR text)
  * txt, rtf, doc

example
----

From Commandline:

    jpeg-handler ./path/to/fun-pic.jpeg --output-format json
    {
      date_taken: 987343... // Unix timestamp
      md5sum: ABE853.... // hexdigest
      sha256sum: FEC74D... // hexdigest
    }

As a library

    pic = handleJpeg(read(filename), { defer_checksum: true })
    puts pic.stream.checksum
    puts pic.meta.title
    puts pic.toJSON


The handler should be able to checksum the JPEG stream (not including the embedded JPEG thumbnail which may exist).


Media Database Framework & Server
====

Python / SQLAlchemy  PostgreSQL is probably the best fit for this part of the project.

For each handler there should be a python class which integrates with SQLAlchemy to populate a database.
PostgreSQL has a non-blocking adapter for NodeJS, which other parts of the project is written in.

Relationships:

  * A file has one stream
  * A stream has many meta-data
  * A meta-data has many files
  * A meta-data has many streams
  
meta-data has sub meta-data.
Example. A song has an album and an artist

Optimizations:

  * All searchable strings are also stored in a searchable format (described below in the scratchpad)

Application
----

The server should probably be implemented in Twisted as a simple HTTP server.

  * Resources map to types - audio, video, 
  * Queries allow the 
  * GET /audio/<some_media_id> retrieves file metadata as JSON
  * GET /video/?=lady%20gaga returns a list of results as JSON
  * POST /NEW uploads the file and adds it to the database

Scratchpad and Psuedo-Code
====

filescan:

Step through each file and operate on it

    class filescan:
      moved_files = []
      handlers = []

      function filescan(path):
        for filename, filepath in fs.search(path):
          for handler in handlers:
            handler(filename, filepath)

mediadb: (To be built in python as a unix socket server)

    // http://docs.python.org/library/socket.html#socket-example
    // http://github.com/Kami/python-twisted-binary-file-transfer-demo/blob/master/server.py
    class media:
      plugins = []

      function add(filepath):
        for plugin in plugins:
          plugin.process(filepath)

custom_frontend:

An application build on the framework

    import filescan, media

    function safely_move_file(file, new_file):
      if not fs.exists(new_file):
        fs.mv(file, new_file)
      else
        if same_checksum_on_file(file, new_file):
          fs.mv(file, new_file)
        else  
          safely_move_file(file, new_file + '_copy')

    function force_utf8(path):
      if not utf8_safe(filename):
        new_file = utf8_replace_invalid_chars(filename, '_')
      return new_file

    function main:
      filescan.add_handler(force_utf8)    
      mediadb.move_files = 'move' // 'copy', 'hardlink', 'symlink'
      filescan.add_handler(mediadb)
      filescan.filescan('~/')
      filescan.filescan('~/')
      query = ... 
      // see http://www.sqlalchemy.org/docs/05/ormtutorial.html#querying
      result = mediadb.session.query(query)
      result.toJSON();
      

Database
====

The database primarily stores 3 types of information:

  * File meta-data (including data checksum)
  * Media meta-data (including data checksum)
  * Contextual data

**Strings** should always be either **unicode (UTF-8)**.

**Search Optimization**:

Searchable strings should also be stored reverse in lowercase, sometimes with punctuation removed.

File meta data
----

File meta-data is used to **detect duplicates** and to **track revisions**.

Example Schema:

    CREATE TABLE IF NOT EXISTS Files (
        `id` INTEGER PRIMARY KEY,
        `viewable_id` INTEGER,
        `UUID` CHAR,
        `size` INTEGER,
        `md5sum` CHAR,
        `path_searchable` VARCHAR,
        `filename_searchable` VARCHAR,
        `extension` VARCHAR,
        `ctime` INTEGER,
        `mtime` INTEGER,
        `atime` INTEGER,
        `record_last_updated` DATE,
        UNIQUE (`path`,`filename`)
    )
    CREATE_TABLE IF NOT EXISTS Files_viewable (
        `path` TEXT,
        `filename` TEXT,
        `sha256sum` CHAR,
        `uid` INTEGER,
        `gid` INTEGER,
    )
    CREATE INDEX IF NOT EXISTS pathIndex ON Files(`path_searchable`)
    CREATE INDEX IF NOT EXISTS filenameIndex ON Files(`filename_searchable`)
    CREATE INDEX IF NOT EXISTS md5Index ON Files(`md5sum`)

**Search Optimization**:

  items not likely to be used for search or sort are stored in a separate table and lazy-loaded when needed
  `filename_searchable` - limited to 255 chars, reverse, lowercase
  `path_searchable` - limited to 255 chars, reverse, lowercase, '/' removed
