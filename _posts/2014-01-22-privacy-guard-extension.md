---
layout: post
title: "Privacy Guard chrome extension"
date: 2014-01-22 16:01
comments: true
categories: javascript development chrome-extension privacy
---

Today I am releasing a very simple Chrome extension, [Privacy Guard](https://chrome.google.com/webstore/detail/privacy-guard/edcajbacgdcdeecojhlllbgingbjfbmk).

Recently there have been a few articles about malware extensions in
the Chrome Web Store. Of course, malware and spyware has been rampant
in the Chrome Web Store for quite a while already.

- [Article on OMGChrome](http://www.omgchrome.com/chrome-users-start-raise-awareness-extensions-known-peddle-adware/)
- [Article on Android Authority](http://m.androidauthority.com/google-chrome-ad-injecting-extensions-337074/)

Lifehacker recently featured [an
article](http://lifehacker.com/chrome-protector-notifies-you-if-youre-running-an-adwa-1505371480?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+lifehacker%2Ffull+%28Lifehacker%29)
about an extension "ExtShield" that notifies you if you have one of a
set of known malicious extensions installed.

The funny thing about ExtShield is that this extension could very well
be malware itself. For it requests the most dangerous permission an
extension can ask for (Access to all data on all websites), and it has
no reason to ask for this permission. It simply does not need it in
order to function.

I have had the idea to do a simple Chrome Extension that simply identifies which installed extensions have "high risk" permissions.

So I am announcing [Privacy Guard](https://chrome.google.com/webstore/detail/privacy-guard/edcajbacgdcdeecojhlllbgingbjfbmk), a free extension in the Chrome Web Store. I am releasing it now with a minimal set of functionality because it is, I believe, more useful than something like ExtShield. For one, the [source code](https://github.com/kzahel/privacy-guard-extension) is freely available, and it also does not make use of hard-coded lists of known "bad" extensions. It simply helps you by giving you all the information in a glance and lets you filter extensions and apps by their "risk level"

For example, the Pinterest Pin it Button Chrome extension is a high-risk app, because it requests the "Access your data on all websites" permission. This app is the perfect example of an app that is flagrantly requesting more permissions than it needs. The proliferation of apps that request these permissions where they are clearly not needed (they can use a browser action instead) contributes to numbing users to the gravity of the permission grant.

The "Access your data on all websites" permission means that the extension can steal any authentication tokens, capture every keystroke on every website, and do so rather undetected.

So, make your reasonable voice heard, and re-evaluate whether you need to have high risk extensions installed. At the very least, take a look at who is publishing the extension and make a judgement as to whether you can trust the publisher.

I have a few ideas for more features to add to the Privacy Guard extension. I want to store the publisher data and be able to notify the user when an extension's publisher is changed. I also would like to be able to mark certain publishers as exempt (for example, if you want to trust all Google apps and extensions).

tldr; Give [Privacy Guard](https://chrome.google.com/webstore/detail/privacy-guard/edcajbacgdcdeecojhlllbgingbjfbmk) a try.