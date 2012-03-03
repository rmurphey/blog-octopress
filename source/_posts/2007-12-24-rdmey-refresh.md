--- 
layout: post
date: "2007-12-24"
title: rdmey refresh
comments: true
categories: blogging, css, front-end development, standards, themes, wordpress
---

When I started this about a month ago, I just grabbed a template that didn't look too awful, changed a few colors, got rid of all the pixel-based font-sizing, and hoped that no one would look under the hood until I had some time to do it myself from scratch. With the holidays here, I finally had the chance, and I spent a couple hours this afternoon tearing down the theme I had and building my own. Gone are gems like

<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>


which the old theme used to create space between posts; I've wrapped each post in

<div class="CodeRay">
  <div class="code"><pre>...</pre></div>
</div>


instead. Each page has an h1 on it now that contains the blog title and, for single-post pages, the title of the post as well. <a href="http://marcus.net/blog/">Marcus</a> will be glad to note that the layout is not fixed-width, and it's now nice and readable on my iPhone. I also got rid of nifty class names like "serif," cleaned up the PHP a bit ... all in all, I shaved the css down from 5.1k to 3k, and dropped the size on most of the PHP template files by about 15 percent.

I don't claim this is anything gorgeous; mostly I hope that it's decently good markup, that it's easy to navigate, and that it puts the focus on the content where it belongs.