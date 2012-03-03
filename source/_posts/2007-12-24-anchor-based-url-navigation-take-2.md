--- 
layout: post
date: "2007-12-24"
title: Anchor-based URL navigation, Take 2
comments: true
categories: anchor, jquery, navigation, plugins, progressive enhancement
---

<em>Update: I encourage everyone who arrives at this post to check out <a href="http://benalman.com/projects/jquery-bbq-plugin/">Ben Alman's jQuery BBQ plugin</a> -- it provides a ton of functionality above and beyond what this plugin offers, is much more robust, and is built to take advantage of the latest version of jQuery. While you're welcome to use the plugin on this page, it is no longer supported.</em>

I noticed I was getting a lot of visits via Google for my post on <a href="http://blog.rebeccamurphey.com/2007/12/04/anchor-based-url-navigation-with-jquery/">anchor-based URL navigation with jQuery</a>, so I decided to write a plugin that would accomplish the same thing.

<ul>
<li><a href="http://rebeccamurphey.com/jquery/anchorNav/jquery.anchorNav-0.1.js">Download the plugin</a></li>
<li><a href="http://rebeccamurphey.com/jquery/anchorNav/anchors.html">View a demo</a></li>
</ul>

Call this function on an element that contains "panels" which, in turn, contain anchors. The container element should contain ONLY related panels, as this plugin will show only one first-child element of the container at once.

This plugin will look at the current URL and see whether it contains an anchor. For example:

<div class="CodeRay">
  <div class="code"><pre>http://www.mysite.com/index.html#panel1</pre></div>
</div>


If it finds an anchor, it will look for the panel that contains the associated anchor tag; it will show this panel using the function defined by the <strong>showFn</strong> option, and it will hide the panel's siblings using the function defined by the <strong>hideFn</strong> option.

If the <strong>nav</strong> option is set, it will use the nav option setting as a selector to locate the page's navigation. It will look for a link with an href matching the anchor; if it finds a matching link, it will run the function defined by the <strong>currentNavFn</strong> option on the link element (useful for setting the current nav item).

If the <strong>nav</strong> option is set, it will also set up onclick functions on each of the links in the nav that refer to anchors on the page; the onclick function will use the <strong>show</strong> and <strong>hide</strong> options to show and hide the associated panels.

<h3>Options</h3>
<ul>
<li>
<strong>showFn</strong> function to show the current panel</li>
<li>
<strong>hideFn</strong> function to hide the current panel's siblings</li>
<li>
<strong>nav</strong> selector for the page's navigation section</li>
<li>
<strong>currentNavFn</strong> function to run on the link in the nav that is associated with the anchor</li>
<li>
<strong>anchorClass</strong> class assigned to anchor tags; <em>setting this will improve the speed on pages with lots of links</em>
</li>
<li>
<strong>noAnchorFn</strong> function to run if the URL does not contain an anchor
</li>
</ul>

Default option values:

<div class="CodeRay">
  <div class="code"><pre>var options = {
  showFn: function() { $(this).show(); },
  hideFn: function() { $(this).hide(); },
  nav: null,
  currentNavFn:
    function() {
      // this will add a class to the parent of the
      // link that matches the currently selected anchor
      $(this).parent().siblings().removeClass('anchor-nav-current');
      $(this).parent().addClass('anchor-nav-current');
    },
  anchorClass: null,
  noAnchorFn:
    function() {
      // this will show the first panel
      // if the URL doesn't contain an anchor
      $container.children().hide().eq(0).show();
    }
};</pre></div>
</div>