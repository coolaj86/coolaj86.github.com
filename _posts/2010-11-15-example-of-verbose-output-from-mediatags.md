---
layout: article
uuid: 550DAC09-7980-495C-95B9-AE2AE35BF31F
title: Example of verbose output from MediaTags
name: example-of-verbose-output-from-mediatags
created_at: 2010-11-15
updated_at: 2010-11-15
categories: mediatags
---

The represents what output from `m4atags`, part of `mediatags`, should look like.

Literal Tags:

    m4atags --literal ~/Music/Cascada/Evacuate\ the\ Dancefloor/01\ Evacuate\ the\ Dancefloor\ \(Radio\ Edit\).m4a

    {
      "---- com.apple.iTunes;iTunSMPB": "00000000 00000840 00000028 00000000008D8B98 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000",
      "---- com.apple.iTunes;iTunNORM": "000017D8 0000182D 0000659D 00006F6B 0000687D 0000687D 00007321 000072AA 0000DC97 0000BF91",
      "©nam": "Evacuate the Dancefloor (Radio Edit)",
      "©ART": "Cascada",
      "aART": "Cascada",
      "©alb": "Evacuate the Dancefloor",
      "gnre": "Dance",
      "trkn": "1 of 10",
      "disk": "1 of 1",
      "cpil": false,
      "pgap": false,
      "©day": "2009-07-14T07:00:00Z",
      "apID": "coolaj86@gmail.com",
      "cprt": "℗ 2009 Robbins Entertainment LLC",
      "cnID": "322203070",
      "rtng": "Inoffensive",
      "atID": "27456059",
      "plID": "322202899",
      "geID": "17",
      "sfID": "United States (143441)",
      "akID": "0",
      "stik": "Normal",
      "purd": "2010-09-17 17:00:45",
      "covr": true,
      "---- com.apple.iTunes;iTunMOVI": "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<!DOCTYPE plist PUBLIC \"-//Apple//DTD PLIST 1.0//EN\" \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">\n<plist version=\"1.0\">\n<dict>\n  <key>asset-info</key>\n  <dict>\n    <key>file-size</key>\n    <integer>7485659</integer>\n    <key>flavor</key>\n    <string>2:256</string>\n  </dict>\n</dict>\n</plist>"
    }


Verbose Tags:

    m4atags --verbose ~/Music/Cascada/Evacuate\ the\ Dancefloor/01\ Evacuate\ the\ Dancefloor\ \(Radio\ Edit\).m4a

This output was adapted from `AtomicParsley`. Some problems exist:

  * The `---` tag should be grouped like either the example above or `"---- com.apple.iTunes;iTunSMPB": ...`
  * Where possible the text data in the tags should be output as well.
    * i.e. `{ "value": { "data": { "text": "Cascada", "start": ..., "length": ..., "end": ... } } }`

    {
        "ftyp": {
            "start": 0,
            "length": 32,
            "end": 32 
        },
        "moov": {
            "start": 32,
            "length": 225082,
            "end": 225114,
            "value": {
                "mvhd": {
                    "start": 40,
                    "length": 108,
                    "end": 148 
                },
                "trak": {
                    "start": 148,
                    "length": 71105,
                    "end": 71253,
                    "value": {
                        "tkhd": {
                            "start": 156,
                            "length": 92,
                            "end": 248 
                        },
                        "mdia": {
                            "start": 248,
                            "length": 71005,
                            "end": 71253,
                            "value": {
                                "mdhd": {
                                    "start": 256,
                                    "length": 32,
                                    "end": 288 
                                },
                                "hdlr": {
                                    "start": 288,
                                    "length": 34,
                                    "end": 322 
                                },
                                "minf": {
                                    "start": 322,
                                    "length": 70931,
                                    "end": 71253,
                                    "value": {
                                        "smhd": {
                                            "start": 330,
                                            "length": 16,
                                            "end": 346 
                                        },
                                        "dinf": {
                                            "start": 346,
                                            "length": 36,
                                            "end": 382,
                                            "value": {
                                                "dref": {
                                                    "start": 354,
                                                    "length": 28,
                                                    "end": 382,
                                                    "value": {
                                                        "url ": {
                                                            "start": 370,
                                                            "length": 12,
                                                            "end": 382 
                                                        } 
                                                    }
                                                } 
                                            }
                                        },
                                        "stbl": {
                                            "start": 382,
                                            "length": 70871,
                                            "end": 71253,
                                            "value": {
                                                "stsd": {
                                                    "start": 390,
                                                    "length": 32871,
                                                    "end": 33261,
                                                    "value": {
                                                        "mp4a": {
                                                            "start": 406,
                                                            "length": 32855,
                                                            "end": 33261,
                                                            "value": {
                                                                "esds": {
                                                                    "start": 442,
                                                                    "length": 51,
                                                                    "end": 493 
                                                                },
                                                                "pinf": {
                                                                    "start": 493,
                                                                    "length": 32768,
                                                                    "end": 33261 
                                                                } 
                                                            }
                                                        } 
                                                    }
                                                },
                                                "stts": {
                                                    "start": 33261,
                                                    "length": 24,
                                                    "end": 33285 
                                                },
                                                "stsc": {
                                                    "start": 33285,
                                                    "length": 40,
                                                    "end": 33325 
                                                },
                                                "stsz": {
                                                    "start": 33325,
                                                    "length": 36264,
                                                    "end": 69589 
                                                },
                                                "stco": {
                                                    "start": 69589,
                                                    "length": 1664,
                                                    "end": 71253 
                                                } 
                                            }
                                        } 
                                    }
                                } 
                            }
                        } 
                    }
                },
                "udta": {
                    "start": 71253,
                    "length": 153861,
                    "end": 225114,
                    "value": {
                        "meta": {
                            "start": 71261,
                            "length": 153853,
                            "end": 225114,
                            "value": {
                                "hdlr": {
                                    "start": 71273,
                                    "length": 34,
                                    "end": 71307 
                                },
                                "ilst": {
                                    "start": 71307,
                                    "length": 151759,
                                    "end": 223066,
                                    "value": {
                                        "----": {
                                            "start": 71315,
                                            "length": 188,
                                            "end": 71503,
                                            "value": {
                                                "mean": {
                                                    "start": 71323,
                                                    "length": 28,
                                                    "end": 71351 
                                                },
                                                "name": {
                                                    "start": 71351,
                                                    "length": 20,
                                                    "end": 71371 
                                                },
                                                "data": {
                                                    "start": 71371,
                                                    "length": 132,
                                                    "end": 71503 
                                                } 
                                            }
                                        },
                                        "----": {
                                            "start": 71503,
                                            "length": 162,
                                            "end": 71665,
                                            "value": {
                                                "mean": {
                                                    "start": 71511,
                                                    "length": 28,
                                                    "end": 71539 
                                                },
                                                "name": {
                                                    "start": 71539,
                                                    "length": 20,
                                                    "end": 71559 
                                                },
                                                "data": {
                                                    "start": 71559,
                                                    "length": 106,
                                                    "end": 71665 
                                                } 
                                            }
                                        },
                                        "©nam": {
                                            "start": 71665,
                                            "length": 60,
                                            "end": 71725,
                                            "value": {
                                                "data": {
                                                    "start": 71673,
                                                    "length": 52,
                                                    "end": 71725 
                                                } 
                                            }
                                        },
                                        "©ART": {
                                            "start": 71725,
                                            "length": 31,
                                            "end": 71756,
                                            "value": {
                                                "data": {
                                                    "start": 71733,
                                                    "length": 23,
                                                    "end": 71756 
                                                } 
                                            }
                                        },
                                        "aART": {
                                            "start": 71756,
                                            "length": 31,
                                            "end": 71787,
                                            "value": {
                                                "data": {
                                                    "start": 71764,
                                                    "length": 23,
                                                    "end": 71787 
                                                } 
                                            }
                                        },
                                        "©alb": {
                                            "start": 71787,
                                            "length": 47,
                                            "end": 71834,
                                            "value": {
                                                "data": {
                                                    "start": 71795,
                                                    "length": 39,
                                                    "end": 71834 
                                                } 
                                            }
                                        },
                                        "gnre": {
                                            "start": 71834,
                                            "length": 26,
                                            "end": 71860,
                                            "value": {
                                                "data": {
                                                    "start": 71842,
                                                    "length": 18,
                                                    "end": 71860 
                                                } 
                                            }
                                        },
                                        "trkn": {
                                            "start": 71860,
                                            "length": 32,
                                            "end": 71892,
                                            "value": {
                                                "data": {
                                                    "start": 71868,
                                                    "length": 24,
                                                    "end": 71892 
                                                } 
                                            }
                                        },
                                        "disk": {
                                            "start": 71892,
                                            "length": 30,
                                            "end": 71922,
                                            "value": {
                                                "data": {
                                                    "start": 71900,
                                                    "length": 22,
                                                    "end": 71922 
                                                } 
                                            }
                                        },
                                        "cpil": {
                                            "start": 71922,
                                            "length": 25,
                                            "end": 71947,
                                            "value": {
                                                "data": {
                                                    "start": 71930,
                                                    "length": 17,
                                                    "end": 71947 
                                                } 
                                            }
                                        },
                                        "pgap": {
                                            "start": 71947,
                                            "length": 25,
                                            "end": 71972,
                                            "value": {
                                                "data": {
                                                    "start": 71955,
                                                    "length": 17,
                                                    "end": 71972 
                                                } 
                                            }
                                        },
                                        "©day": {
                                            "start": 71972,
                                            "length": 44,
                                            "end": 72016,
                                            "value": {
                                                "data": {
                                                    "start": 71980,
                                                    "length": 36,
                                                    "end": 72016 
                                                } 
                                            }
                                        },
                                        "apID": {
                                            "start": 72016,
                                            "length": 42,
                                            "end": 72058,
                                            "value": {
                                                "data": {
                                                    "start": 72024,
                                                    "length": 34,
                                                    "end": 72058 
                                                } 
                                            }
                                        },
                                        "cprt": {
                                            "start": 72058,
                                            "length": 58,
                                            "end": 72116,
                                            "value": {
                                                "data": {
                                                    "start": 72066,
                                                    "length": 50,
                                                    "end": 72116 
                                                } 
                                            }
                                        },
                                        "cnID": {
                                            "start": 72116,
                                            "length": 28,
                                            "end": 72144,
                                            "value": {
                                                "data": {
                                                    "start": 72124,
                                                    "length": 20,
                                                    "end": 72144 
                                                } 
                                            }
                                        },
                                        "rtng": {
                                            "start": 72144,
                                            "length": 25,
                                            "end": 72169,
                                            "value": {
                                                "data": {
                                                    "start": 72152,
                                                    "length": 17,
                                                    "end": 72169 
                                                } 
                                            }
                                        },
                                        "atID": {
                                            "start": 72169,
                                            "length": 28,
                                            "end": 72197,
                                            "value": {
                                                "data": {
                                                    "start": 72177,
                                                    "length": 20,
                                                    "end": 72197 
                                                } 
                                            }
                                        },
                                        "plID": {
                                            "start": 72197,
                                            "length": 32,
                                            "end": 72229,
                                            "value": {
                                                "data": {
                                                    "start": 72205,
                                                    "length": 24,
                                                    "end": 72229 
                                                } 
                                            }
                                        },
                                        "geID": {
                                            "start": 72229,
                                            "length": 28,
                                            "end": 72257,
                                            "value": {
                                                "data": {
                                                    "start": 72237,
                                                    "length": 20,
                                                    "end": 72257 
                                                } 
                                            }
                                        },
                                        "sfID": {
                                            "start": 72257,
                                            "length": 28,
                                            "end": 72285,
                                            "value": {
                                                "data": {
                                                    "start": 72265,
                                                    "length": 20,
                                                    "end": 72285 
                                                } 
                                            }
                                        },
                                        "akID": {
                                            "start": 72285,
                                            "length": 25,
                                            "end": 72310,
                                            "value": {
                                                "data": {
                                                    "start": 72293,
                                                    "length": 17,
                                                    "end": 72310 
                                                } 
                                            }
                                        },
                                        "stik": {
                                            "start": 72310,
                                            "length": 25,
                                            "end": 72335,
                                            "value": {
                                                "data": {
                                                    "start": 72318,
                                                    "length": 17,
                                                    "end": 72335 
                                                } 
                                            }
                                        },
                                        "purd": {
                                            "start": 72335,
                                            "length": 43,
                                            "end": 72378,
                                            "value": {
                                                "data": {
                                                    "start": 72343,
                                                    "length": 35,
                                                    "end": 72378 
                                                } 
                                            }
                                        },
                                        "----": {
                                            "start": 72378,
                                            "length": 397,
                                            "end": 72775,
                                            "value": {
                                                "mean": {
                                                    "start": 72386,
                                                    "length": 28,
                                                    "end": 72414 
                                                },
                                                "name": {
                                                    "start": 72414,
                                                    "length": 20,
                                                    "end": 72434 
                                                },
                                                "data": {
                                                    "start": 72434,
                                                    "length": 341,
                                                    "end": 72775 
                                                } 
                                            }
                                        },
                                        "covr": {
                                            "start": 72775,
                                            "length": 150291,
                                            "end": 223066,
                                            "value": {
                                                "data": {
                                                    "start": 72783,
                                                    "length": 150283,
                                                    "end": 223066 
                                                } 
                                            }
                                        } 
                                    }
                                },
                                "free": {
                                    "start": 223066,
                                    "length": 2048,
                                    "end": 225114 
                                } 
                            }
                        } 
                    }
                } 
            }
        },
        "free": {
            "start": 225114,
            "length": 370847,
            "end": 595961 
        },
        "mdat": {
            "start": 595961,
            "length": 6889698,
            "end": 7485659 
        }
    }
