--- 
layout: post
date: "2007-12-25"
title: Flexible plugin for nested table of contents
comments: true
categories: jquery, plugins, table of contents, toc
---

My partner saw the search results page for this blog and, always on the lookout for a way to improve things, suggested that it would benefit from a table of contents at the top. I could do this with Wordpress, of course, but it sounded like a good idea for a jQuery plugin, too.

A little <a href="http://www.google.com/search?q=jquery+table+of+contents+plugin&amp;ie=utf-8&amp;oe=utf-8&amp;aq=t&amp;rls=org.mozilla:en-US:official&amp;client=firefox-a">googling</a> showed that there were a couple of <a href="http://nepherim.lifehaiku.com/jqtoc/">existing</a> <a href="http://www.packtpub.com/article/using-jquery-script-for-creating-dynamic-table-of-contents">solutions</a>, but there were two things I wanted that they didn't seem to offer:

<ul>
<li>the ability to select via selectors exactly which elements would be included (i.e., <code>#content h2</code> instead of just <code>h2</code>)</li>
<li>the ability to nest the lists of sections to reflect the hierarchy of the page</li>
</ul>

After puzzling a bit over <a href="http://groups.google.com/group/jquery-en/browse_thread/thread/bd63d1b8f24be124">how to get a list of selected elements in the order they appear in the document</a> (see note below), I wrote my own plugin.

<ul>
<li><a href="http://rebeccamurphey.com/jquery/toc/jquery.toc-0.1.js">Download the plugin</a></li>
<li><a href="http://rebeccamurphey.com/jquery/toc/toc.html">View a demo</a></li>
<li><a href="http://plugins.jquery.com/project/toc">jQuery plugin site</a></li>
</ul>

Usage:

<div class="CodeRay">
  <div class="code"><pre>var $toc = $.toc('#content h1, h2, h3.foo');</pre></div>
</div>


So you can do:

<div class="CodeRay">
  <div class="code"><pre>$.toc('h1,h2,h3').prependTo('body').attr('id','toc');</pre></div>
</div>


<h3>Note</h3>
<code>$('h1,h2')</code> will return a jQuery object containing all the h1's in a document, followed by all the h2's in a document: <code>h1 h1 h1 h2 h2 h2</code>. I needed all the h1's and h2's in the document in the order they appeared; so, perhaps, <code>h1 h1 h2 h2 h1 h2 h2</code>.

The obvious-in-hindsight answer:

<div class="CodeRay">
  <div class="code"><pre>$('h1,h2').addClass('selectedElements');
var selectedElements = $('.selectedElements');</pre></div>
</div>


Of course, the bad thing about this is that, on pages with lots of elements, it's going to be slow. Any better suggestions?