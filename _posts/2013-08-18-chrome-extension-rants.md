---
layout: post
title: "Chrome extension rants, optional permissions, and chrome.tabs"
date: 2013-08-18 10:53
comments: true
categories: chrome, extension, development
---

If you are the type that writes extensions that request access to
"access my browsing tabs and history" or "all data on all websites" or
"all data on my computer and all websites I visit" then please stop
reading now. Go ahead and request your permissions and I will happily
refuse to accept them.

For people like myself, slightly more mindful of exposing myself to
unnecessary security risks, please read on. The main reason why chrome
extensions are so tricky is because [optional
permissions](http://developer.chrome.com/extensions/permissions.html#manifest)
are a relatively new feature, and many of the APIs are simply easier
to use when you have full access to everything.

I have been hacking on a chrome extension for the past few days for a
[hack project](https://github.com/kzahel/spotify-web-extension). I
have not written an extension before (having only done chrome platform
apps
(i.e. [jstorrent](https://chrome.google.com/webstore/detail/jstorrent/anhdpjpojoipgpmfanmedjghaligalgb). Compared
to packaged apps, chrome extensions are _hard_. Content scripts are a
pretty weird thing, and I am spending as much time on stack overflow
and [chromium issues](code.google.com/p/chromium/issues) as I am in my editor.


Some things I've learned:

chrome.tabs is a complex beast
------------------------------

  chrome.tabs is available even without any special permissions. But
  you won't know the URLS for updated tabs. If your extension wants
  access to only a single domain (i.e. www.myapp.com) and you want to
  inject content scripts there, I found it wasn't reliable doing it in
  the manifest with the "matches". Perhaps I could write a test app
  and file a bug report. But this method is not flexible enough anyway.

  Rather than using the manifest origin matching to inject content
  scripts, doing it manually allows for added flexibility. With
  chrome.tabs.executeScript, you can have your extension request
  additional access (say &lt;all_urls&gt;") and now inject on those
  pages. This is not possible with manifest based pattern matching.

  Each time you see a chrome.tabs.onUpdated, attempt to execute code
  from your (background) event page



{% highlight javascript %}

// keep a "PID" for the background page, so that the content script
// can know if the background page was updated. Maybe also include the
// version number, so a content script can know if the extension was
// updated.
var backgroundPID = Math.floor(Math.random() * Math.pow(2,31))

chrome.tabs.onUpdated.addListener( function(tabId, changeInfo, tab) {
  var updateInfo = {pid:backgroundPID,
                    event:'onUpdated',
                    changeInfo:changeInfo,
                    tabInfo:tab.status}
  chrome.tabs.executeScript(tabId, 
                            {code: "var updateInfo="+
                                    JSON.stringify(updateInfo)+
                                    ";[updateInfo,window.location.origin]" }, 
                            function(results) {
    if (results === undefined) {
       // we didn't have access to this tab's origin
    } else {
       var injectedContentScriptInfo = results[0]
       // then we have window.location.origin information 
       // for all content scripts which we were allowed to access

       // now inject the actual content script, which will have access to relevant updateInfo 
       // (so it will know not to run twice, etc)
       chrome.tabs.executeScript(tabId, {file: "your_content_script.js"}, function(content_result) {
          // do other things if you want
       })

    }
  }
}

    {% endhighlight %}


Of course this method has a distinct disadvantage. If you look in your
background page's console logs, you'll see a lot of errors "Cannot
access contents of url..."

There is a chrome.tabs.query which will return a list of tabs matching
your host query, but it has no edge triggered option. Perhaps if you
[star this issue](https://code.google.com/p/chromium/issues/detail?id=264704) we
will see some improvement on this front.

[Event pages](http://developer.chrome.com/extensions/event_pages.html) are cool
----------------------------

From that page: "the performance advantages are significant,
especially on low-power devices." I wonder if this means extensions
on ARM devices, like androids is to come in the near future.

