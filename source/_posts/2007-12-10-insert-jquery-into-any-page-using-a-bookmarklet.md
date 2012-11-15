---
layout: post
date: "2007-12-10"
title: Insert jQuery into any page using a bookmarklet
comments: true
categories:
  - bookmarklet
  - front-end development
  - javascript
  - jquery
---

Ever wish you could use jQuery on a page at the drop of a hat, without having access to the page itself? Well I'll be damned, <a href="http://simonwillison.net/2007/Aug/15/jquery/">you can</a>.

(This is one of those things that in hindsight seems so painfully obvious that I'm almost embarrassed to admit that it never occurred to me, but admit it I will.)

For those working with jQuery 1.2.1, here is an <a>updated Insert jQuery 1.2.1 bookmarket</a>, and here is the code for the bookmarklet in its entirety:

{% codeblock lang:javascript %}
javascript:void(function(){var s=document.createElement('script'); s.src='http://code.jquery.com/jquery-1.2.1.js'; document.getElementsByTagName('head')[0].appendChild(s);}())
{% endcodeblock %}

I came across this in a recent post at <a href="http://morethanseven.net/posts/debug-web-pages-with-jquery-and-jash">Morethanseven</a>, but the Simon Willison post is from August, so this is probably a bit of old news. I'm still excited though.
