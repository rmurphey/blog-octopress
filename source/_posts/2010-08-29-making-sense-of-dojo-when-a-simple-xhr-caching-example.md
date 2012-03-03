---
layout: post
date: "2010-08-29"
title: "Making sense of dojo.when: A simple XHR caching example"
comments: true
categories: deferreds, dojo, dojo.when, promises
---

<p>Right before Dojo 1.5 came out, the Sitepen blog had <a href="http://www.sitepen.com/blog/2010/05/03/robust-promises-with-dojo-deferred-1-5/">a great post</a> about the improvements 1.5 would bring for <code>dojo.Deferred</code>. The part that really caught my eye was <a href="http://docs.dojocampus.org/dojo/when"><code>dojo.when</code></a>, a method that lets you pass a value to a function whether that value is available now or as the result of some asynchronous operation. Either way, you get a &ldquo;promise&rdquo; that <em>when</em> the value is available, the function you provided will run.</p>

<p>This is one of those things that was super-neat when I read about it, but it took me a while to incorporate it into my code &mdash; it&rsquo;s only in the last couple of weeks that I&rsquo;ve had that wonderful moment when I&rsquo;ve said &ldquo;oh, I could totally use <code>dojo.when</code> for that!&rdquo; Moments like these make me very happy.</p>

<p>It&rsquo;s pretty common that an application makes an Ajax request for some data, and then caches that data so the request won&rsquo;t have to happen again; the pattern might look something like this:</p>

{% codeblock lang:javascript %}
var myCache = {};

function getSomeStuff(stuffId) {
  if (myCache[stuffId]) {
    handleResponse(myCache[stuffId]);
    return;
  }

  dojo.xhrGet({
    url : 'foo.php',
    content : { id : stuffId },
    load : function(response) {
      myCache[stuffId] = response;
      handleResponse(response);
    }
  });
}
{% endcodeblock %}


<p>Here we have a function that takes an ID; the function looks in the cache to see if there&rsquo;s a value stored for the ID, and if so, it passes the stored value to a <code>handleResponse</code> function. If not, it does an XHR to get the data; when the XHR succeeds, it stores the data in the cache and, again, passes the value to the <code>handleResponse</code> function.</p>

<p>There&rsquo;s nothing strictly wrong with this, but I discovered that some neat abstraction opportunities became more clear when I switched to using <code>dojo.when</code> instead:</p>

{% codeblock lang:javascript %}
var myCache = {};

function getSomeStuff(stuffId) {
  dojo.when(
    myCache[stuffId] || dojo.xhrGet({
      url : 'foo.php',
      content : { id : stuffId },
      load : function(response) {
        myCache[stuffId] = response;
      }
    }),
    handleResponse
  );
}
{% endcodeblock %}


<p>Now we&rsquo;re telling our <code>getSomeStuff</code> function to look for a cached value; if it finds one, <code>dojo.when</code> will immediately pass that value to the <code>handleResponse</code> function. If it doesn&rsquo;t find one, it will run the XHR, and <code>dojo.when</code> will magically pass the XHR&rsquo;s response to the <code>handleResponse</code> function instead. This is hot.</p>

<p>This works because <code>dojo.xhrGet</code> returns a &ldquo;promise&rdquo; object with a <code>then</code> method. <code>dojo.when</code> looks to see whether it got a promise object as its first argument; if so, it uses the <code>then</code> method of the promise object to attach the callback provided as the second argument to <code>dojo.when</code>. If not, it simply calls the callback immediately on the first argument. The real magic is actually in <code>dojo.Deferred</code>, not in <code>dojo.when</code> itself. Since all of Dojo&rsquo;s XHR methods return a <code>dojo.Deferred</code> promise, <code>dojo.when</code> will &ldquo;just work.&rdquo;</p>

<p>I found that I was going through my application and ripping out instances of the old code, replacing it with the new. And then I had that &ldquo;oh sh*t I&rsquo;m copying and pasting, aren&rsquo;t I &hellip;&rdquo; moment, and saw my way to an abstraction.</p>

<p>In my application, I was actually caching the responses using the URL from which I&rsquo;d requested them, which works out to be a perfectly unique ID for the data. (This particular part may or may not work in your application.) My abstraction was an essentially drop-in replacement for <code>dojo.xhrGet</code> calls:</p>

{% codeblock lang:javascript %}
var cache = {};

function cacheableXhrGet(settings) {
  var url = settings.url,
      req = cache[url] ||
            dojo.xhrGet(dojo.mixin({
              // override the load handler
              load : function(resp) {
                cache[url] = resp;
              }
            }, settings));

  dojo.when(req, settings.load);
  return req;
}
{% endcodeblock %}


<p>I can pass a settings object to <code>cacheableXhrGet</code> that looks exactly like the object I&rsquo;d pass to <code>dojo.xhrGet</code>, but replace the <code>load</code> function before actually passing it to <code>dojo.xhrGet</code>. But before the XHR even has a chance to get set up, I check my cache for a stored response; if I find one, I store it in the <code>req</code> variable, but otherwise I store the XHR there.</p>

<p>In either case, the function defined at <code>settings.load</code> gets the proper response value via <code>dojo.when</code>. For bonus points, I then return either the cached value or the XHR &mdash; which means <em>other code can use the return value of <code>cacheableXhrGet</code> for its own <code>dojo.when</code></em>. How neat is that?</p>

<h2>Conclusion</h2>

<p>Promises and deferred&rsquo;s are a really pleasant tool to have in your JavaScript arsenal once you get the hang of them, and <code>dojo.when</code> seems like a great place to start understanding them.</p>

<p>Out of the box, Dojo makes use of deferreds for all of its XHR functionality, meaning that you can pass around the return value of any Dojo XHR method and do fun things you can&rsquo;t do with jQuery&rsquo;s <code>$.ajax</code>, like add more callbacks to a request after you&rsquo;ve set it up.</p>

<p>I&rsquo;ve just recently started realizing when I could incorporate <a href="http://docs.dojocampus.org/dojo/Deferred"><code>dojo.Deferred</code></a> functionality into my own code &mdash; again, now that I&rsquo;ve got the hang of it, I&rsquo;m pretty sure it&rsquo;s going to dramatically change how I write asynchronous code.</p>

<p><em>Disclaimer: This post contains sample code for illustration purposes. In reality it&rsquo;s all namespaced and these naked functions are actually methods in classes and stuff, and the real code doesn&rsquo;t even look much like the code you see here. I&rsquo;ve also completely ignored questions of when to clear or invalidate the cache. You&rsquo;ve been warned.</em></p>
