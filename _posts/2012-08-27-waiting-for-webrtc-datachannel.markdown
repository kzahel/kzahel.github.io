---
layout: post
title: "waiting for WebRTC DataChannel"
date: 2012-08-27 11:17
comments: true
categories: 
---
I have been spending some time recently with Websockets, and while they're quite powerful (the latest spec supports sending javascript byte arrays), they still have one rather limiting problem: two browsers still cannot communicate directly to each other.

I work with peer-to-peer software as a part of my job. Having WebRTC's DataChannel API available would make my life such a pleasure.

<http://stackoverflow.com/questions/10218250/transfering-json-between-browsers-with-webrtc>

According to some comments on that page, I may have something to work with by the end of the year. Here's hoping!

It seems like there may be some very unexpected and important privacy implications. Browsers have never had true peer-to-peer capabilities before.

I hope nobody worries too hard about this and the first implementations are simply pulled into the Chromium source.
