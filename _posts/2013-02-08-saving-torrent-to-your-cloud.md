---
layout: post
title: Saving a torrent to your cloud
category: programming
tags: jstorrent
---

I finally have a working prototype of saving a torrent to your cloud disk. The motivation for this was to be able to consume torrent content while on an iOS device, such as an iPad. Check out <a href="http://jstorrent.com">JSTorrent.com</a>. (Note: the iOS App store does not approve of "torrent" apps, so we need to put the app in the browser instead)

On your first visit, you'll be prompted to sign into your google account. Don't worry, it will only ask for access to write and read from files in its own sandbox. It doesn't even ask for your identity or other information.

It's a major pet peeve of mine when applications ask for permission to do a lot more than I care to let them have access to, so rest assured I am not interested in spying around your files, and I don't even ask that you trust me that I won't.

Anyway, the drill is as per usual. Find a torrent URL somewhere on the web, paste it into the text input box at the top, and you'll now be saving the files not to your local disk, but be streaming them directly to your google drive. Currently any gmail user will have a google drive automatically, I think it gives you something like 2GB for free (Then $2.50/month for 100GB)

You can inspect the upload progress by clicking on the files tab. There's a column that represents the fraction of data for that file that's been uploaded to the cloud drive.

Once it's saved, you'll see icons for opening and saving the files from the cloud drive.

I've started to add support to saving to your DropBox, which has a similar client side javascript library.

All hail the static web app that uses javascript APIs.

Next time -- will be adding WebRTC DataChannel functionality so you can share files directly with other browsers.