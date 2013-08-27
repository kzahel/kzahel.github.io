---
layout: post
title: "Use yielding (await) in javascript today, with traceur!"
date: 2013-08-27 12:01
comments: true
categories: es6 javascript development
---

Javascript is pretty annoying sometimes, especially when writing a lot of callbacks.
If you're feeling adventurous, try out the ultra awesome [traceur compiler](https://github.com/google/traceur-compiler) which lets you use ECMAScript Harmony features today.

Boilerplate code (traceur.js and bootstrap.js from traceur)
{% highlight html %}
<html>
<head>
    <script src="https://traceur-compiler.googlecode.com/git/bin/traceur.js"
        type="text/javascript"></script>
    <script src="https://traceur-compiler.googlecode.com/git/src/bootstrap.js"
        type="text/javascript"></script> 
    <script>
      traceur.options.experimental = true;
    </script>
</head>
<body>
<script type="text/traceur">
{% endhighlight %}

The "traceur.options.experimental" is to enable the "await" keyword, which is what we're going to show off.

And now some boilerplate code to wrap the async XMLHttpRequest into a "Deferred" (a Promise). 

{% highlight javascript %}
var simpleGet = function(url) { 
  var deferred = new Deferred(); // traceur defines this
  var xhr = new XMLHttpRequest(); 
  xhr.onreadystatechange = function() { 
    if (xhr.readyState == 4) { 
      deferred.callback(xhr);
    } 
  }; 
  xhr.open('GET', url, true); 
  xhr.send(); 
  return deferred; 
}; 



{% endhighlight %}

Now here's the cool part, that we've been waiting for. You can now write
code that looks synchronous, but doesn't block the event loop. This is
a definite readability win, as opposed to minor gains you get with
things like CoffeeScript.

The Grand Finale
====

{% highlight javascript %}
var url = 'http://example.com/api';
function run() { 
  var result;
  await result = simpleGet(url);
  console.log('GOT IT...', url, result);
}

run() 

{% endhighlight %}

If you're already compiling your application into javascript, you
might as well do it with traceur and get some actual cool features (unlike
CoffeeScript, which only gives you ambiguous looking syntax that omits
parenthesis with function calls, ick)

Have fun, and be safe!

Here is what the compiled code for _run()_ ends up looking like:

{% highlight javascript %}
function run() {
  var $that = this;
  var $state = 4;
  var $storedException;
  var $finallyFallThrough;
  var result;
  var $value;
  var $err;
  var $result = new Deferred();
  var $waitTask;
  var $G = {
    GState: 0,
    current: undefined,
    yieldReturn: undefined,
    innerFunction: function($yieldSent, $yieldAction) {
      while (true) switch ($state) {
        case 4:
          ;
          $state = 5;
          break;
        case 5:
          console.log('REQUESTIN...');
          $state = 7;
          break;
        case 7:
          $waitTask = simpleGet(url);
          $waitTask.then($createCallback(1), $createErrback(2));
          return;
          $state = 1;
          break;
        case 1:
          result = $value;
          $state = 3;
          break;
        case 2:
          throw $err;
          $state = 3;
          break;
        case 3:
          debugger;
          $state = 9;
          break;
        case 9:
          console.log('GOT IT...', url, result);
          $state = 11;
          break;
        case 11:
          $result.callback(undefined);
          $state = -2;
          break;
        case -2:
          return;
        case -3:
          $result.errback($storedException);
          $state = -2;
          break;
        default:
          throw "traceur compiler bug: invalid state in state machine" + $state;
      }
    },
    moveNext: function($yieldSent, $yieldAction) {
      while (true) try {
        return this.innerFunction($yieldSent, $yieldAction);
      } catch ($caughtException) {
        $storedException = $caughtException;
        switch ($state) {
          default:
            $state = -3;
            break;
        }
      }
    }
  };
  var $continuation = $G.moveNext.bind($G);
  var $createCallback = function($newState) {
    return function($0) {
      $state = $newState;
      $value = $0;
      $continuation();
    };
  };
  var $createErrback = function($newState) {
    return function($0) {
      $state = $newState;
      $err = $0;
      $continuation();
    };
  };
  $continuation();
  return $result.createPromise();
}
{% endhighlight %}

Good thing we did not have to write that code!

Make sure to go check out the [traceur project on
github](https://github.com/google/traceur-compiler) and show your
support for their badassery.