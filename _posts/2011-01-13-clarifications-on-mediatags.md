---
layout: article
uuid: F32F3277-68D1-49C4-A1C0-43F83949B4B2
title: Clarifications on MediaTags
name: clarifications-on-mediatags
created_at: 2011-01-13
updated_at: 2011-01-13
categories: mediabox
---

Speed, Completeness, and Identification are the three most important qualities of this application.

Speed

  * The end product is "MediaBox", a small 1GHz OMAP3530 which will use MediaTags on 30,000+ files.

Completeness

  * It should be possible to represent every meta-data tag present in the file.

  * It should be able to read tags in an unabstracted manner

  * pure binary tags, such as Album Art, may also be extracted as JSON
    * --extract-binary-tags=bas64 -- included in JSON
            Ex: "@art": "Az48tks9cC...."

    * --extract-binary-tags-to=path/to/attachments-dir -- placed into the folder and referenced in JSON
            Ex: "@art": "./path/to/attachments-dir/my song.m4a.@art.jpeg"

    * Binary tag extraction will usually be a post-processing feature and should be off by default

  * Both stream and media metadata shoud be present, but separate.

Identification - Checksums of the "stream" part of a file

  * Each tag application should be able to produce a checksum of the stream (data) portion of a file
    * --with-stream-md5sum
    * --with-stream-sha2sum
  * The checksum should not be of the file as a whole
  * The checksum should not include the tags
  * The checksum is probably from the byte offset of the last header tag to the end of the file or first header tag

Conculsions

TagLib - Let's use taglib if

  * taglib allows raw access to tags (I believe it does)
  * taglib can generate the same detail of information as AtomicParsley

Mutagen - Probably not a good fit

  * mutagen is significantly slower than taglib ?
  * mutagen does not allow access to all tags, just abstracted normalized ones ?

Libexiv2 - yes

Exiftool - probably not a good fit

  * Is it fast? if not, no
  * does it allow access to all tags? or does it abstract them?

Type Detection - GNU `file` is too slow!

  * my tests show that `file ./my-song.m4a` takes more time than `AtomicParsely -t ./my-song.m4a`
    * detecting the file type should not take more time than parsing the file!

  * type detection should be very very simple
    * if the file has an extension, use the extension to determine the type
      * if it doesn't match a known type, ignore it

  * if the file doesn't have an extension (very rare), try matching the first few bytes of the header
    *  it's okay to use `file` for rare cases - little time will be wasted in comparison.

  * fail with error if the file cannot be parsed as expected
    * some media types can have multiple types of tags (id3, m4a, musepak, oggtag?, etc?)
      * try the most likely first (mp3 -> id3)
    * some media types can have embedded tags
      * mp3 -> album art -> jpeg -> exif
      * only parse the intended type
      * don't parse exif data from an mp3

What does Unix Filter Class mean?

  * Incidentally, UNIX filter applications are the ones that you described as taking input data from a Pipe (as in cat abc.mp3 | m4atags --verbose)

Priority

While I was waiting my friend created prototypes for outputting mp3 and m4a media metadata which I am using for now.

The most important thing that I need right now is to be able to checksum the data stream.

It's okay to rearrange some of the other things if it's better for your workers' workflow,
but I would like the checksum-ing ability first.

Once the --literal-tags is done I'll know better what the --normalized-tags should look like

   1. Stream (not file) checksums
         1. jpegtags --without-metadata --with-sha256sum ./my-file.jpeg
         2. mp3tags --without-metadata --with-sha256sum ./my-file.mp3
         3. aactags --without-metadata --with-sha256sum ./my-file.m4a

            { "stream": { "sha256sum": "ae68f......" } }

   2. JPEG Media metadata --literal-tags
         1. exivtags ./my-file.jpeg
         2. xmptags ./my-file.jpeg
         3. iptctags ./my-file.jpeg

   3. Stream metadata
         1. jpegtags
         2. aactags
         3. mp3tags

   4. Media --literal-tags
         1. m4atags
         2. id3tags

   5. Media --verbose-tags
         1. m4atags
         2. id3tags
         3. exivtags
         4. xmptags
         5. iptctags

   6. eBook/pdf tags
         1. more information about what information is stored and can be extracted is needed

Before --normalized-tags I first want to see the outputs of the stream and meta-data --literal-tags
I've pushed --binary-tags to be a future consideration

General Clarifications

Meta-data organization

I want to make it clear that there are three types of meta data that I am particularly interested in.

Media (tag) metadata

  * The tags that universally describe a particular piece of artwork / media
    * Music (id3, m4a): artist, album, track number, rating
    * Images (exif, ipic, xmp): geo location, keywords, aspect ratio, date/time taken, visual similarity metrics
    * Documents (proprietary): author, title / subject, keywords, text body

Stream (data) metadata

  * The tags that describe a specific stream of media, but not the artwork / media itself
    * Music (aac, mp3): md5sum, stream type (aac, mp3), quality, bitrate
    * Images (jpeg): md5sum, stream type, quality, width, height, color depth
    * Documents (odxml, msxml, pdf): md5sum, stream type (xml, ms-binary), word count, page count

File (data + tag) metadata - not necessary to analyze at this time

  * Tags that describe a set of bytes on disk
    * --with-file-metadata
    * All Types: md5sum, access time, modified time, size, inode count


Examples

  * I have an mp3 and an m4a of the same song.
    * The media metadata will be almost exactly the same
      * the exception is that some of tag formats support more options than others

    * The stream metadata will almost always be different
      * an exception may be that the bitrates are the same

    * The file metadata will be almost always be different

Text

  * Many of the files to be processed will contain Chinese, Japenese and other international characters
    * UTF-8 should be used, not ASCII alone.
    * <strike>UTF-16 may also be used</strike> -- JSON doesn't support UTF16
    * --pretty-print should output with pretty whitespacing -- somewhat like JSON.stringify(object, null, "\t")

Modularity

The most important parts of the organization are this

  * The library should be modular, I prefer small bits of code that each do one thing well

  * It should be easy to build just one feature of the application or incorporate it in another application

  * mediatags `/my-song.mp3 --with-stream-tags --with-md5sum` gives the combined result of
    * `id3tags /my-song.mp3`
    * `mp3tags --with-md5sum /my-song.mp3`

  * `m4atags /my-song.mp3` returns `{ "error": "no m4a tags found" }`

  * `aactags /my-song.mp3` returns `{ "error": "no aac stream found" }`

  * In the future I would like to create a MediaTags plugin for Node.JS


A possible organization

  * mediatags - single binary that handles any type of file
    * libmediatags.o
      * libmediatagsid3.c
      * libmediatagsm4a.c
      * libmediatagsexiv.c
      * libmediatagsmp3.c
      * libmediatagsaac.c
      * libmediatagsjpeg.c
      * libmediatagspdf.c
      * libmediatagsdoc.c
      * libmediatagsodt.c
    * id3tags ---> mediatags (symlink)
    * m4atags --> mediatags (symlink)
    * etc
  * Each lib has a method such as getMediaTags(), getStreamTags()

Future Considerations

Ideas to consider, but not to implement yet.

Binary Tags

  * perhaps Google Protobuf?

Streaming Input

  * Accept data in chunks over a socket ? 
