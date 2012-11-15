---
layout: post
date: "2007-12-09"
title: Using ems for font sizing in css
comments: true
categories:
  - css
  - front-end development
  - standards
  - thoughts
---

A List Apart had a great article recently on <a href="http://www.alistapart.com/articles/howtosizetextincss/">using 'em' for CSS font-size declarations</a>, which served as great back-up for some conversations I'd been having among coworkers. (It turns out that people who didn't have a former life in print don't necessarily understand what an em is: a self-referential unit of font size measurement, equal to the height of the capital letter M. Back in the typesetting days, it was a unit for measuring space, especially horizontal space, in the form of an "em dash" (rendered now as <code>&amp;mdash;</code>) or an "em space." There was also a sister unit, the "en.")

Anyway: I just came across <a href="http://benjaminsterling.com/using-em-for-font-size-learning-on-the-go/">this post</a>, by someone trying to put ems into practice; at the end, he gets into tools for doing conversions from pixels to ems, and it struck a surprising nerve with me.

Here's the thing: While I can see how pixel conversion seems useful when you first make the switch from px to em, I think the whole point of using ems instead of pixels is to embrace the concept that, on the web, sizes are relative, not absolute. If you're focused on matching an absolute size in a mockup by converting pixels to ems, the chances seem good that you're dealing with a layout that wasn't intended to work with relatively sized text to begin with, and/or that your ultimate product won't work if the user makes a different decision than you about how they will consume it.

If you receive a Photoshop mockup where body copy is 10px tall, I think it's a tremendous waste of time to measure that 10px and convert it to ems -- 10px on my 1024x768 monitor is a far cry from 10px on my 1600x1200 monitor, nevermind on the HDTV sitting across the room.

When you're designing and styling text for the web, better to:

<ul>
<li>Assume from the start that users will consume your site differently than you do.</li>
<li>Make decisions in your design, CSS and HTML that will consistently convey the content and hierarchy of your site, regardless of how it is consumed.</li>
<li>Assume that the user knows better than you what a good base text size is for them.</li>
<li>Use a reset styleheet — I like <a href="http://meyerweb.com/eric/thoughts/2007/05/01/reset-reloaded/">Eric Meyer's CSS Reset Reloaded</a> — to set everything to that base size.</li>
<li>Vary the sizes of elements <em>relatively</em> using ems.</li>
</ul>