---
layout: post
title: "mozilla falling behind?"
date: 2012-08-27 14:18
comments: true
categories: 
---
I thought I would see how Mozilla's browser would work with my new app using the HTML5 FileSystem API.

I install Firefox. Eww, no retina support still. Okay... let's see how the app works.
Oops... Mozilla hasn't implemented the FileSystem API yet. I was led to a discussion on <https://hacks.mozilla.org/2012/07/why-no-filesystem-api-in-firefox/>.

It seems Mozilla is a bit behind! The arguments against the FileSystem API are weak. No file locking? Not interested! All the operations are asynchronous anyway. Oh, you have a competing effort underway, the "Web API"? First off, that is a terrible name for an API. So generic. Second, why are you guys documenting some C++ classes for some dreamed-up API instead of participating in WHATWG discussions?

Still, I'm interested in seeing what they come up with. But I suspect they won't have enough manpower to come up with anything more compelling than what's already been implemented under Chromium