---
layout: post
title: Torrent in a google chrome packaged app.
category: coding
tags: bittorrent, html5, chrome
---

I finally released something based on the <a href="https://github.com/kzahel/jstorrent">jstorrent code</a>. 

You can download the <a
href="https://chrome.google.com/webstore/detail/jstorrent/anhdpjpojoipgpmfanmedjghaligalgb">packaged app</a> from
google play. If you're not aware, packaged apps are like browser
extensions, only they get their own top level window. As of the date
of this writing, they are not searchable within the google app store
that you find in your chrome browser, but should be at some point.

It's pretty neat, having access to raw sockets. The goal of jstorrent is to work with any type of connection, be it websockets, webrtc, or raw sockets. I like the abstraction of an "IOStream" that I picked up while working with <a href="https://github.com/facebook/tornado">tornado</a>.

{% highlight javascript %}

    function IOStream(host, port) {
        this.host = host;
        this.port = port;
        this.sockno = null;
        this._closed = null;
        this._connected = false;
        this._created = false;
        chrome.socket.create('tcp', {}, _.bind(this.oncreate, this));
    }

    IOStream.prototype = {
        oncreate: function(data) {
            this.sockno = data.socketId;
            _.bindAll(this, 'onconnect','do_read','send','onsend','got_read');
            chrome.socket.connect( this.sockno, this.host, this.port, this.onconnect );
        },
        onconnect: function(data) {
            if (this._closed) {
                return;
                // BUG in implementation
            } else {
                this.onopen(data);
                this.do_read();
            }
        },
        do_read: function() {
            assert(! this._closed)
            assert(! this._connecting);
            chrome.socket.read( this.sockno, 4096, this.got_read );
        },
        got_read: function(data) {
            if (this._closed) {
                return;
                // BUG in implementation
            } else if (data.resultCode < 0) {
                this.doclose();
            } else {
                this.onmessage({data:data.data});
                // somehow onmessage can close the connection... (bad data?)
                if (! this._closed) {
                    this.do_read();
                }
            }
        },
        send: function(data) {
            chrome.socket.write( this.sockno, data, this.onsend );
        },
        onsend: function(result) {
            if (result.bytesWritten > 0) {
            } else {
                this.doclose('did not write all data');
            }
        },
        doclose: function(reason) {
            this.onclose({});
            if (this._connected) {
                chrome.socket.disconnect(this.sockno);
            }
            chrome.socket.destroy(this.sockno);
        },
        close: function(reason) {
            this._closed = true;
            // cancel pending read?
            this.doclose('called close');
        },
    }

{% endhighlight %}

Neat. The chrome.socket implementation seems to be pretty undocumented
at this point so I had to make a few guesses with the edge cases but
this class lets me use it in about the same way I would use a
websocket stream.

Currently I'm working on adding functionality to download to a cloud
storage provider (Google drive first, Dropbox and others later) so
that browsers without filesystems can still consume torrents. It's
kind of a pain, because pieces can span multiple files and I want to
make sure I don't have any memory leaks. Working with streams is
rather tricky. And handling error states with resumable uploads will
add even more complexity.

After that, implementing a WebRTC stream class will be a game-changer!
Well, maybe. At least it'll be a good example use case for
WebRTC. Really though, I think the best use cases for WebRTC are real
time games. It would be neat for example to see Warcraft II ported to
the browser and maybe have the connection to your friend be initiated
like it was back in the good old days, by dialing a phone number. The
browser based version should make the modem baudrate handshaking song,
too, for added effect.
