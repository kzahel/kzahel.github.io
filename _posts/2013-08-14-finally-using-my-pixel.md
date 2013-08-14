---
layout: post
title: "finally using my pixel!"
date: 2013-08-14 10:11
comments: true
categories: pixel, chromeos, development
---

The other day I stayed home nursing my tennis back and had left my work computer (a macbook pro) at work, so I had to make do with my secondary computer, a chromebook pixel I picked up at Google I/O.

I really liked the hardware of the machine and when I first got it I tried crouton but was annoyed that the touchscreen didn't work very well in X11. You have to run ChromeOS's fancy window manager system (called Aura) and a special Chrome browser build to enjoy all the benefits of the multitouch screen.

I did manage to settle on a development environment that suits me alright.

First of all, enable developer mode. Then follow crouton instructions to setup your linux "VM" (a chroot actually) at [crouton](https://github.com/dnschneid/crouton).

I recommend setting a chromeos root password as well as using encryption for your chroot for added security. (otherwise any other local user would be able to inspect the data in your chroot, which means anyone with physical access would be able to look at your shit pretty easily)

Now you'll want a terminal: [hterm/crosh window](https://chrome.google.com/webstore/detail/crosh-window/nhbmpbdladcchdhkemlojfjdknjadhmh)

Open your terminal (ctrl-alt-T in Chrome) and open the developer tools, and go to the javascript REPL. Disable the audible bell

```
term_.prefs_.set('audible-bell-sound', '')
```

If you're an emacs user, you'll want to set some other options ( found from [hterm FAQ](http://git.chromium.org/gitweb/?p=chromiumos/platform/assets.git;a=blob;f=chromeapps/hterm/doc/faq.txt;h=f0d3007f9fc54c7331f68293b7fae5f5b71214ff;hb=95f6a2c7a984b1c09b7d66c24794ce2057144e86) )

```
term_.prefs_.set('alt-backspace-is-meta-backspace',true)
```

That makes emacs alt-backspace actually delete a word.

```
term_.prefs_.set('font-size', 13)
```

I found the default font size of 15 to be too large.

Now you'll probably want to use tmux because it's such a pain to bring up more terminal windows inside your chroot.



{% highlight bash %}
# inside your ~/.tmux.conf
# allow scrolling through history buffers
set-window-option -g mode-mouse on
# I use C-b a lot inside emacs, so 
# rebind tmux to use C-t, which I never use
unbind C-b
set -g prefix C-t
{% endhighlight %}

If you want to use an X11 program from aptitude ( say you download an
MKV using
[jstorrent](https://chrome.google.com/webstore/detail/jstorrent/anhdpjpojoipgpmfanmedjghaligalgb)
and want to use VLC to watch it), you can do so using the useful
"host-x11" command that prints out some environment variables that you
can set and then run the program in the current X server.