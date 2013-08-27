---
layout: post
title: "Spotify skip track chrome extension"
date: 2013-08-15 14:07
comments: true
categories: chrome, development
---

_Update_ - the more advanced extension ([Spotify Web Extension](https://chrome.google.com/webstore/detail/spotify-web-extension/gkmfagbigbkgjbbphlemmafhjabeofek) has remote-control abilities. It uses Google Cloud Messaging to setup a push channel. When you want to remote control another computer, my server wakes up your extension using the channel, and then sets up a relay between the two (a WebSocket connection on both ends).

----

I just published a very simple extension to skip to the next track in the [spotify web player](https://play.spotify.com). Here is the 
[chrome web store link](https://chrome.google.com/webstore/detail/cioodogigbffcemogihoifckgacjaakc). The source code is also on [github](https://github.com/kzahel/skip-track-extension).

Make sure to set a keyboard shortcut for easiest use (go to the chrome extensions page (type chrome://extensions in the URL Bar) and on the bottom click "Keyboard shortcuts". Enjoy!

I'm working on a more [featurefull extension](https://github.com/kzahel/spotify-web-extension) that will allow you to do more than just skip tracks :-)