---
layout: post
title: "Chrome fileSystem notes"
date: 2014-02-26 10:00
comments: true
categories: javascript development chrome-app
---

I've been trying to fix a bug in JSTorrent for the past few days. The
problem I'm seeing is that an HTML5 DirectoryEntry reference becomes
"stuck" -- doing any sort of request against it like .getFile() or
.getMetadata() simply does not fire any callbacks. I've rewritten the
whole Disk I/O subsystem of JSTorrent, hoping to get better insight
into which conditions trigger this "broken" state.

One thing I've started doing is keeping a cache of FileEntry objects
and re-using those. That seems to work pretty well. I haven't run into
any issues re-using a FileEntry reference, even when the underlying
file changes due to other processes writing to or truncating the file.

However, if you do the same thing to the result of a FileEntry.file()
(the actual File object on which you can do .slice() and use a
FileReader to do reads), the reference becomes invalid once the
underlying file is modified in any way (even "touch"ing the file will
make the file reference invalid)

Originally I wanted to use the synchronous versions of all these APIs
because that would have been much simpler. But unfortunately there is
no way to pass a FileEntry to a web worker,
(https://code.google.com/p/chromium/issues/detail?id=242373), and you
need a FileEntry to be able to do file operations (currently you call
FileEntry.createWriter and get a FileWriter object).

Experiment: what happens when you have a cached DirectoryEntry and
somebody moves the file? It fires the error callback to whatever you
try to do on it.