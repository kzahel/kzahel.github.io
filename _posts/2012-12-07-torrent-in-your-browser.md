---
layout: post
title: BitTorrent video streaming in your browser
category: coding
tags: bittorrent, html5
---

This post contains outdated information! Proceed reading with a grain of salt. I've abandoned trying to get torrents working on the open web platform :-)

I started working on an HTML5 torrent client a few months ago when I
had the realization that if enough BitTorrent clients would accept
WebSocket connections, it would be possible to download pieces
directly from a desktop torrent client to your web browser. There
would be several advantages to being able to participate it BitTorrent
swarms in the “naked” web. By naked, I mean without any special
plugins or software. A whole new set of users could participate in the
BitTorrent ecosystem. From those who are unable and who are unwilling
to install custom desktop software, to those who are stuck in
<a href="http://www.apple.com">walled gardens</a>, but especially for
those who don't know or don't care what a torrent is.

There were a number of challenges I encountered when developing the
prototype. The most difficult problem is the lack of wide browser
support for the FileSystem API. Google Chrome provides this very
convenient and stable and usable interface. The only complaint I have
is that it does not support sparse files (i.e. seeking far into a file
and writing without having to manually fill in a lot of null bytes). I
was very disappointed when I read <a
href="https://hacks.mozilla.org/2012/07/why-no-filesystem-api-in-firefox/">Mozilla’s
arguments against the implementation</a>. There are polyfills that
make use of IndexedDB, but they have severe limitations for my use
case, which needs to support files up to multiple gigabytes in size.

I went ahead anyway implementing <a href="http://bittorrent.org/beps/bep_0003.html">the protocol</a> and saving files to the
browser sandbox filesystem, intent on supporting only the Chrome
browser for the time being. The <a
href="https://github.com/kzahel/jstorrent">code to the first
prototype</a> of the browser torrent client is available. You can see
the app hosted at <a href="http://jstorrent.com">jstorrent.com</a>. It
also can be loaded as a Chrome packaged app and will make use of the
chrome experimental socket API.


I currently work at <a href="http://bittorrent.com">BitTorrent</a>,
which maintains the popular <a
href="http://utorrent.com">&micro;Torrent</a> client. Updates to these
clients are slow and methodical, so most clients as of this date are
still unable to accept Websocket connections (this will be rolling out in version
3.3). If you have been following the latest developments with WebRTC,
you'd know that Firefox and Chrome are rolling out the DataChannel
API, which will also allow for browsers to connect directly to each
other. You can bet &micro;Torrent will learn to speak WebRTC, too.

For purposes of demonstration, I decided to write a small embeddable
html page that attempts to stream a video from a BitTorrent swarm
directly in your browser. Since it is still not possible to connect
directly to most swarms until &micro;Torrent 3.3 usage is widespread, these make use
of a custom proxy server that behaves much like <a
href="https://github.com/kanaka/websockify">websockify</a>.

{% highlight javascript %}

<iframe width="500" height="300" 
src="http://jstorrent.com/examples/embed_window.html#hash=f8049a655273f6a000be2bb2119716bcd4c741bc" 
frameborder="0" allowfullscreen="1"></iframe>

{% endhighlight %}

Embed window:
<div id="click" style="border:1px dashed red; padding: 1em">Click to load embed window (Chrome only)</div>
<script src="/scripts/torrent-show.js">
</script>

Basically, we are downloading data from the swarm and saving it to the HTML5 FileSystem storage, then creating a video element and sourcing it to that file.

Since the file is not completely downloaded, the video element will not be immediately playable. Some javascript code running in a web worker attempts to parse the MP4 box format (borrowed code from <a href="https://github.com/mbebenita/Broadway/blob/master/Player/mp4.js">broadway</a>, see this <a href="http://haxpath.squarespace.com/imported-20100930232226/2011/10/28/broadwayjs-h264-in-javascript.html">fascinating article</a>). We download, somewhat unintelligently, from the beginning and end of the file in order to get the important video metadata information that the webkit video player needs to be able to start playing.

When seeking to somewhere in the middle of the file, we need to manually calculate using the mp4 keyframe and time to sample tables which byte offsets the video player will actually use, and prioritize downloading that data from the swarm.

The implementation is very much at an early prototype stage, but it would be interesting to see how good the performance could be if each stage in this process were optimized. For example, it would be better to make use of the new <a href="http://dvcs.w3.org/hg/html-media/raw-file/tip/media-source/media-source.html">MediaSource API</a> being developed by Microsoft, Google, and Netflix. And it would be much better if every torrent video was encoded in multiple bitrates so that it could be compatible with MPEG-DASH.

I am currently working on a prototype to add support to other browsers (such as iOS Safari) that lack the FileSystem API. Instead of saving back to a local disk (which happen to be relatively small on mobile devices anyway), it would save to Google Drive or Dropbox's multipart upload APIs.

If this sounds interesting to you, I would tell you that BitTorrent is
a great place to work. BitTorrent provides options, not rules.
