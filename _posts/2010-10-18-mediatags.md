---
layout: article
uuid: DB119187-9172-4842-B3BD-88479C4FE609
title: MediaTags
name: mediatags
created_at: 2010-10-18
updated_at: 2010-10-18
categories: unfinished
---

Job Description

Project Summary
====

Description:

MediaTags will be an Open Source collection of libraries written in C / C++ for reading meta-data from various media types and outputting the results as JSON. Each library will have an executable that reads from `stdin` or a file (and perhaps a socket).

Existing Open Source libraries should be used where possible.

This collection of tools will run on a library of over 30,000 files.
Each file should take less than 0.05 seconds to process where possible.
The system on which these utilities will primarily be used is a 600MHz OMAP3530 (TI ARM) running OpenEmbedded Linux.
It may be desirable to daemonize the main process to avoid the overhead cost of repeated start-up.


Existing tools in higher-level languages such as `hachoir` or `mutagen` (python) are probably not acceptable because of the start-up cost.

The code will be hosted either on GitHub or a private gitosis repository using `git`.

Please continue reading the specs below.
In your reply, please include the number of *hours* you expect the project to take and what you think will be most interesting to you to code.

Thank you,
AJ ONeal



Project Details
====

Listed below are the names of the executables which should be part of this collection, along with existing libraries that may be adaptable.

Media meta-data
----

These utilties extract *media* meta-data, not specefic to the particular file.

  * id3tags (adaptable from mp3info, libid3)
  * m4atags (adaptable from AtomicParsley)
  * xmptags (libexiv2)
  * iptctags
  * exivtags
  * pdftags - including OCR (PDFBox?)
  * doctags

Embedded data (such as album art) should be included as base64 or optionally extracted into a file.

Examples:

Normalized Fortmat:

    cat ~/Music/Just\ Dance.m4a | m4atags - # read from a buffer
    # or
    m4atags ~/Music/Just\ Dance.m4a --normalized-tags # read from a file
      {
        title: "Just Dance",
        artist: [
          "Lady Gaga",
          "Space Cowboy"
        ],
        songwriter: "Tyson Ritter",
        album: "Move Along",
        genre: "Alternative & Punk",
        "apple_id": "tom.harrison@email.com",
        "rating": "Inoffensive",
        // lyrics, base64 images, and so forth
      }

Default Fortmat:

    m4atags ~/Music/Just\ Dance.m4a --literal-tags # use the literal tagnames
      [
        "¬©nam": "Just Dance",
        "¬©ART": [
          "Lady Gaga",
          "Space Cowboy"
        ],
        "¬©wrt": "Tyson Ritter",
        "¬©alb": "Move Along",
        "¬©day": "2000-07-11T07:00:00Z",
        "apID": "tom.harrison@email.com",
        // etc
      ]

Verbose Format:

    m4atags ~/Music/Just\ Dance.m4a --verbose-tags # use the literal tagnames
      [
        {
          "tag": "¬©nam",
          "start_bit": "0x6E",
          "length": 11,
          "name": "title",
          "value": "Just Dance"
        },
        {
          "tag": "¬©ART",
          "start_bit": "0x77",
          "length": 24,
          "value": [
            "Lady Gaga",
            "Space Cowboy"
          ]
        }
        // etc
      ]

Binary Format:

    m4atags ~/Music/Just\ Dance.m4a --binary-tags /path/to/file.bin # use the literal tagnames

Perhaps Google protobuf

    TODO give example protobuf format

or a simple C/C++ object with version info.

Althought this example is given in psuedo-C,
C++ allowance of inheritance for the versions would probably be the best choice.

    #define MT_T_ALBUM   1
    #define MT_T_ALBUM_S "¬©alb"
    #define MT_N_ALBUM_S "album"

    #define MT_T_ALBUM   2
    #define MT_T_ALBUM_S "¬©ART"
    #define MT_N_ALBUM_S "artist"

    // ...

    struct m4atags {
      int version;
    }
    struct m4atags_v1 {
      int version;
      int tag;
      int start_bit;
      int length;
      int name;
      // ...
    }

    read_in_file(m4atag* tag, char* filename);
    switch(tag->version) {
      case 1:
      m4atag_v1 tag1 = (m4atag_v1*) tag;
    }
      

Stream meta-data
----

These utilities extract *stream* meta-data, not information about the media.

  * mp3tags
  * aactags
  * jpegtags

Example:

    aactags ~/Music/Just\ Dance.m4a --with-md5sum --with-sha256sum
      {
        "bitrate": "256",
        "quality": "cbr",
        "duration": "2:46.381",
        "mime": "video/mp4",
        "md5sum": "c40575a7a594ba2d109646d5a6c3ca8a", // checksum of the data stream, not the file
        "sha256sum": "103efcab8cbbee87890facbf01c1404df84f9264dbc8ea658677b905d9b521df",
        "data_offset": "0xFE3C", // the start of the data stream in the file
        "data_end": "0x356EF81", // the end of the data stream
        // and so on
      }

File, Media, and Stream meta-data
----

The main executable, `mediatags`, should gather all possible meta-data using the appropriate child-libraries.

Using `file` for type detection is not acceptable due to the overhead (AtomicParsley can parse all `m4a` tags in less time than it takes `file` to execute.

  * mediatags

Example:

    mediatags ~/Music/Just\ Dance.m4a --file-meta-data=true --stream-meta-data=true --with-stream-checksums --with-media-meta-data
      {
        file: {
          atime: 1193909541, // unix timestamp
          ctime: 1193909541,
          mtime: 1193909541,
          md5sum: "e7444acd4d538ede466c6d6cb932c5ec", // of the whole file, not just the stream
          // etc
        },
        stream: {
          // see above for example
        },
        media: {
          // see above for example
        }
      }


System Specifications
====

This library is designed to be fast as to run well on embedded environments:

It will most likely be prototyped on one of the following COMs:

[Gumstix Overo](http://www.gumstix.com/store/catalog/product_info.php?products_id=256):

  * OS: Angstrom OpenEmbedded Linux
  * GPP: 500MHz (720MHz max)  ARM TI OMAP3530 CPU
  * RAM: 256MB
  * Storage: 2GB microSDHC
  * External Storage: 1TB SATA over USB

[BeagleBoard-XM](http://beagleboard.org/hardware-xM):

  * OS: Angstrom OpenEmbedded Linux
  * GPP: 1GHz max ARM TI DM3730 CPU
  * RAM: 512MB
  * Storage: 2GB microSDHC
  * External Storage: 1TB SATA over USB
