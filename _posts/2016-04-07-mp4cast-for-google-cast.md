---
layout: post
title: "MP4Cast for Google Cast"
date: 2016-04-06
comments: true
categories: javascript chrome-app chromecast cast mp4cast
---

I've just released a new Chrome App, [MP4Cast](https://chrome.google.com/webstore/detail/mp4cast-for-google-chrome/lbfdhgbfobdiokoifnflhlkaphobcgmo). I started working on it when I noticed there was no way for me to stream a locally stored mp4 video from my ARM Chromebook directly to my Google Chromecast™.

Actually, it sort of was possible. I had to spin up [Web Server for Chrome](https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb), select the directory containing the file, then make sure the options were set to allow connections from other computers, then find the exact media URL, and then find a way to tell Chromecast to play that URL. Not a very friendly experience!

So to simplify that, I built MP4Cast.

You can read more about it on the [MP4Cast help page](https://www.mp4cast.com/help.html). You can use it today, all you need to do is visit [www.mp4cast.com](https://www.mp4cast.com)