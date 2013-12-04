---
layout: post
title: "Using Chrome Platform app chrome.fileSystem.retainEntry"
date: 2013-12-04 14:01
comments: true
categories: javascript development chrome-packaged-app
---

I've been tweaking JSTorrent to add support for writing directly to
user selected directories.

In the manifest file, you need to add the "write", "directory", and
"retainEntries" permissions. The documentation is here
http://developer.chrome.com/apps/fileSystem.html.

The tricky part is handling external media. There's a way to get
events when external media is attached and detached. You need to
request "system.storage" permission. Docs here
http://developer.chrome.com/apps/system_storage.html

Unfortunately the directory "Entry" given by
chrome.fileSystem.chooseEntry with the {type:"directory"} option does
not give any information as to whether the directory selected by the
user resides on an external storage or the internal storage of the
device.

This could be a very poor user experience. A torrent job would simply
show some kind of "storage error" and not be able to tell the user
that they need to re-attach a specific external storage device.

The documentation for chrome.* APIs is spartan as usual, so it leaves
developers to explore all the edge cases and make guesses as to
whether these are accidental or intended and with unit tests backing
these behaviors.

When the storage is detached, a call to chrome.fileSystem.restoreEntry
will NOT fail! However, when trying to do some kind of access on the
machine that would require actually accessing the device, such as
trying to read the entry's metadata, you will get the error callback.

So it's at least possible to infer that a given retained Entry may be
from a detachable storage, if doing a getMetadata call returns a
NotFoundError. However this error also occurs when the selected
directory is renamed or removed (incidentally, if a new directory is
created and has the same absolute path on the same disk, it can be
accessed and written to via retainEntry, even though the actual inode
is different)

So the moral of the story is -- to the retainEntry mechanism, there is
really no difference between external storage not being available, and
a directory simply being removed from the SSD/internal disk. I guess
that's pretty elegant, after all. So simply messaging "directory or
external disk missing" is the simplest solution. Maybe only displaying
"or external disk" if chrome.system.storage.getInfo shows there was in
fact an external disk present when the user called chooseEntry.