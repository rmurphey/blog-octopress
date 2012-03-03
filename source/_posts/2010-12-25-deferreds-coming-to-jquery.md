---
layout: post
title: "Deferreds coming to jQuery 1.5?"
date: 2010-12-25
---

_I have updated this post to show the code using the API that was released in jQuery 1.5.0._

A few weeks ago, a [major rewrite of jQuery's Ajax functionality](https://github.com/jquery/jquery/commit/ab3ba4a81252c4357a7aab5f24d765d41d47986e)
landed in the jQuery GitHub repo. Thanks to [Julian Aubourg](http://www.jaubourg.net/), jQuery looks like it will get a feature
that I've desperately wished it had ever since I started spending time with
Dojo:

{% codeblock lang:javascript %}
function doAjax(debug) {
  var req = $.ajax({
    url : 'foo.php',
    dataType : 'json',
    success : function(resp) {
      console.log(resp);
    }
  });

  if (debug) {
    req.success(function(resp) {
      console.log("let's see that again!", resp);
    });
  }

  // return the request object so other
  // things can bind to it too!
  return req;
}

doAjax().success(function(resp){
  console.log("Once more, with feeling!", resp);
});
{% endcodeblock %}

Starting with 1.5 (I'm guessing), users will be able to easily attach callbacks
to XHRs ... later! And pass around the return value of `$.ajax` as an actually
useful object with a familiar API, rather than just getting back the native
XHR! No longer will we have to bundle up callback functionality -- some of
which might be optional, or depend on other code -- inside our success or error
callbacks. So hott.

When I heard that these Ajax changes had landed, I got to thinking about how
Dojo provides its ability to belatedly attach success and error handlers to its
XHRs: underlying its XHR methods is
[dojo.Deferred](http://dojotoolkit.org/reference-guide/dojo/Deferred.html#dojo-deferred).
It **allows users to assign callback functions for success and error conditions
for a task that may not complete immediately.** Dojo makes use of this for its
XHR stuff, but it's incredibly useful generically, too:

{% codeblock lang:javascript %}
function doSomethingAsync() {
  var dfd = new dojo.Deferred();
  setTimeout(function() {
    dfd.resolve('hello world');
  }, 5000);
  return dfd.promise;
};

doSomethingAsync().then(function(resp) {
  console.log(resp); // logs 'hello world'
});
{% endcodeblock %}

So, Dojo provided the late callback functionality via deferreds. jQuery now had
late callback functionality. Was the deferred functionality hidden in the
jQuery Ajax rewrite, waiting to be exposed? Julian and I and several others got
to talking in the jQuery development IRC channel, and decided it seemed like an
interesting and viable idea. A few days later, Julian's [first draft of jQuery.Deferred](https://github.com/jquery/jquery/commit/116c82b027a03a7a5670fa580fa9af819cc1cc03)
landed in a branch on GitHub.

It's early days, but there have been a lot of good discussions already about
the proposed API and how it should work. Through all of the conversations I've
been part of, it's become really clear that no one cares about deferreds until
you show them what they actually mean: the ability to register an interest in
the outcome of arbitrary asynchronous behavior, *even if the outcome has
already occurred*. Even better, you can register your interest in the outcome
of behavior that *may or may not be asynchronous*.

I assure you that once you have experienced this, you will wonder how you lived
without it.

{% codeblock lang:javascript %}
var cache = {};

function doSomethingMaybeAsync(val) {
  if (cache[val]) {
    return cache[val];
  }

  return $.ajax({
    url : 'foo.php',
    data : { value : val },
    dataType : 'json',
    success : function(resp) {
      cache[val] = resp;
    }
  });
}

$.when(doSomethingMaybeAsync('foo'))
  .then(function(resp){
    alert("The value for foo is", resp);
  });
{% endcodeblock %}

It'll also be possible to do something like you see below. I'm not sure what
the exact API will be for creating a generic deferred instance, but I hope it
will be something along these lines:

{% codeblock lang:javascript %}
function doIt() {
  var dfd = new $.Deferred();

  setTimeout(function() {
    dfd.resolve('hello world');
  }, 5000);

  return dfd.promise;
}

doIt().then(function(resp) { console.log(resp); }, errorFn);
{% endcodeblock %}

These changes are sitting in a [branch in the jQuery GitHub repo](https://github.com/jquery/jquery/tree/deferred) as we speak, and I think
it's likely we'll see them move to master sooner than later. It's a nice story
of collaboration and community participation that helped make something good --
the Ajax rewrite -- even better.

It's exciting to see jQuery venture a bit more into the abstract. My
experiences with Dojo core so far make me think there are probably more
opportunities for these sorts of utilities that would be of high value for
a substantial number of jQuery users. On the other hand, one of the constant
themes of our conversations about deferreds has been the potential for
confusion with the new methods. Will the API look familiar and jQuery-like, or
will users be confused about the ability to chain methods on something other
than a jQuery selection? Are there bigger-picture considerations when it comes
to adding new constructors to the jQuery namespace? It'll be interesting to see
how these questions sort themselves out, especially if other similar features
appear that don't fall neatly under the well-established
DOM/Ajax/Events/Effects umbrella.

The [conversation's happening on GitHub](https://github.com/jquery/jquery/commits/deferred) -- I hope you'll
join in.
