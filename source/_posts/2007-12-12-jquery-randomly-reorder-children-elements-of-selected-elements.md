--- 
layout: post
date: "2007-12-12"
title: "jQuery: Randomly reorder children elements of selected elements"
comments: true
categories: front-end development, javascript, jquery, plugins
---

I'm working on a new template for a client, and they asked me to display some list elements in a random order on each page load. I did my Google due diligence, but didn't come up with anything that wasn't slideshow-like -- what I wanted was just something that would show <em>all</em> of the children elements of an element, but in a random order. (In this particular case, the children elements were case case studies.) I was already using jQuery on the page, so I wrote a plugin:

<a href="http://blog.rebeccamurphey.com/wp-content/uploads/2007/12/jqueryreorder.js" title="jquery.reorder.js">jquery.reorder.js</a> (1k)

Here's the code:

<div class="CodeRay">
  <div class="code"><pre>$.fn.reorder = function() {

  // random array sort from
  // http://javascript.about.com/library/blsort2.htm
  function randOrd() { return(Math.round(Math.random())-0.5); }

  return($(this).each(function() {
    var $this = $(this);
    var $children = $this.children();
    var childCount = $children.length;

    if (childCount &gt; 1) {
      $children.remove();

      var indices = new Array();
      for (i=0;i

Thoughts? Suggestions? Let me know.</pre></div>
</div>