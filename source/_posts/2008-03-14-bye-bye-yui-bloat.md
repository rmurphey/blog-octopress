---
layout: post
date: "2008-03-14"
title: Bye bye YUI bloat
comments: true
categories: front-end development, javascript, yui
---

I've been doing some serious work this week cutting down on YUI bloat -- our site was including the entire YUI library at the top of every page, at a cost of more than 200k. Worse, because the library was included at the top of every page, the rest of the page had to wait for it to load before it would render.

First, I switched all of our AJAX to use jQuery, the library we've standardized on going forward -- the fact that we had a wrapper function for all of our AJAX calls made this pleasantly easy. Then, I went through the code and figured out which YUI components were being used where. Once that was done, I was able to roll up a few core YUI files into a file to be included in the global header, and call other components only as needed. Total savings: 170k. Holy crap.

I'd still like to see us do away with YUI all together -- <a href="http://ajaxian.com/archives/ojay-a-chaining-wrapper-for-the-yui">Ajaxian wrote about Ojay the other day</a>, and all I could think was "OMG, you want me to use <em>another</em> library to make this library easier to use? I'll stick with jQuery, thanks" -- but considering how much javascript we'd have to rewrite, I'm at least feeling much better about 38k than 208k.

By the way, <a href="http://marcus.net/blog">Marcus</a> sent me a link to a great article about why this stuff matters: <a href="http://searchsoftwarequality.techtarget.com/tip/0,289483,sid92_gci1301766,00.html">Use "SCORN" to test the front end of a website for performance</a> by Scott Barber. It's made me get serious about cutting down on our number of HTTP requests, and so far, it seems to be making a difference.
