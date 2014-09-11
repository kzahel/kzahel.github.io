---
layout: post
title: "ISO Extractor Chrome app"
date: 2014-09-11
comments: true
categories: javascript chrome-app
---

I have been using a Chromebook for my main computer for a long time now. Every once and a while I have a strange itch I need to scratch. Most recently, I had an ISO file that I needed to extract some files from.

Now of course the ISO 9660 file format is not exactly one that many using ChromeOS need to know about, let alone want extract files from. But for some reason I wanted to write an app that would let me extract the files.


[ISO Extractor - Available on the Chrome Web Store](https://chrome.google.com/webstore/detail/iso-extractor/fcpcpcgghhoelelnmdecoldpaheimogc)

[ISO Extractor Source code on Github](https://github.com/kzahel/iso-extractor)

My first step was to find a nice library that had minimal dependencies and would be easily translated into javascript. I found a [small Python library on github iso9660.py](https://github.com/barneygale/iso9660) which looked perfect. Only it uses generators (yield) semantics for iterating over files, which would not easily translate into javascript. What to do?

I pull out the trusty [google-traceur-compiler](https://github.com/google/traceur-compiler) which lets you compile ECMAScript6 which has generators into standard javascript.

First, I needed to have something that behaves like cStringIO on a file. I need to .seek() and .read() synchronously. Javascript gives us FileReaderSync which must be run in a web worker.

Then, I needed an equivalent to python's "struct" module. I found a [node.js module by tjfontaine](https://github.com/tjfontaine/node-struct) and proceeded to make minor modifications to translate from node's Buffer objects into standard javascript DataView and the .getUint8(position, isLittleEndian) family. You can use my modified [jsstruct.js](https://github.com/kzahel/iso-extractor/blob/master/jsstruct.js) if you ever need to do something similar. I didn't translate the .pack() stuff, only the .unpack() things.

When debugging breakpoints traceur-compiled javascript code that uses generators, it looks like a horrible mess (unreadable state machine case statements). However, if you go to chrome://flags and enable experimental ES6 javascript, you can simply run the generators without the aid of traceur, and you can step through your code normally. Almost. As it turned out, hitting a breakpoint inside a generator function inside a web worker was causing chrome to crash. Maybe this will be fixed in the future.

In the end, I was able to get a directory listing of the files within an ISO, which seemed good enough for a days work, so I released the app and see that it's getting about 10 installs per day. Which means maybe I'll add some more functionality, like the ability to extract and save files from the ISO image.
