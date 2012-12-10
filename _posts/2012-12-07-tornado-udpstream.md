---
layout: post
title: Non-blocking UDP in Tornado web's IOLoop
category: coding
---

I've always enjoyed working with <a href="https://github.com/facebook/tornado">Tornado Web</a>, the python web framework that is centered around the idea of using non-blocking sockets. It is a very versatile framework for working with asynchronous streams and I've used it to write a number of applications, including proxy servers, media streamers, and even BitTorrent clients. But tornado is noticably lacking in UDP support. This is fine, of course. After all, it is an HTTP library.

When you would like to do some non-blocking UDP stuff using tornado, all you need to do is use this custom "UDPStream" class that I wrote. It acts a lot like tornado's IOStream class. 

{% highlight python %}

class UDPStream(object):
    def __init__(self, socket, in_ioloop=None):
        self.socket = socket
        self._state = None
        self._read_callback = None
        self.ioloop = in_ioloop or IOLoop.instance()

    def _add_io_state(self, state):
        if self._state is None:
            self._state = tornado.ioloop.IOLoop.ERROR | state
            self.ioloop.add_handler(
                self.socket.fileno(), self._handle_events, self._state)
        elif not self._state & state:
            self._state = self._state | state
            self.ioloop.update_handler(self.socket.fileno(), self._state)

    def send(self,msg):
        return self.socket.send(msg)

    def recv(self,sz):
        return self.socket.recv(sz)
    
    def close(self):
        self.ioloop.remove_handler(self.socket.fileno())
        self.socket.close()
        self.socket = None

    def read_chunk(self, callback=None, timeout=4):
        self._read_callback = callback
        self._read_timeout = self.ioloop.add_timeout( time.time() + timeout, 
            self.check_read_callback )
        self._add_io_state(self.ioloop.READ)

    def check_read_callback(self):
        if self._read_callback:
            # XXX close socket?
            self._read_callback(None, error='timeout');

    def _handle_read(self):
        if self._read_timeout:
            self.ioloop.remove_timeout(self._read_timeout)
        if self._read_callback:
            try:
                data = self.socket.recv(4096)
            except:
                # conn refused??
                data = None
            self._read_callback(data);
            self._read_callback = None

    def _handle_events(self, fd, events):
        if events & self.ioloop.READ:
            self._handle_read()
        if events & self.ioloop.ERROR:
            logging.error('%s event error' % self)


{% endhighlight %}


The basic use of this class is illustrated in the following code snippet.

{% highlight python %}
import socket
udpsock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udpsock.setblocking(False)
udpsock.connect( ('tracker.openbittorrent.com', 80) )
s = UDPStream(udpsock)
s.send( 'some data' )
data = yield gen.Task( s.read_chunk )

{% endhighlight %}




