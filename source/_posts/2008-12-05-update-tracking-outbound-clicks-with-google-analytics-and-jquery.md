--- 
layout: post
date: "2008-12-05"
title: "Update: Tracking outbound clicks with Google Analytics and jQuery"
comments: true
categories: analytics, jquery
---

A while back I wrote a post about <a href="http://blog.rebeccamurphey.com/2008/01/06/track-outbound-clicks-with-google-analytics-and-jquery/">tracking outbound clicks with Google Analytics</a>; way back then (about 6 months ago), the only event that Google Analytics could track was a pageview. Now that they've introduced the <a href="http://code.google.com/apis/analytics/docs/eventTrackerOverview.html">_trackEvent</a> method of the pageTracker object, events that aren't pageviews don't need to count as pageviews anymore; instead, they can be counted as "events," and they can be categorized and labeled.

Here's an updated example of how to track outbound clicks using Google Analytics and jQuery. You'll of course need to be including the "new" analytics code (ga.js, not urchin.js) for this to work, as well as the jQuery library.

<div class="CodeRay">
  <div class="code"><pre>$('a').click(function() {
  var $a = $(this);
  var href = $a.attr('href');

  // see if the link is external
  if ( (href.match(/^http/)) &amp;&amp; (! href.match(document.domain)) ) {

    // if so, register an event
    var category = 'outgoing'; // set this to whatever you want
    var event = 'click'; // set this to whatever you want
    var label = href; // set this to whatever you want

    pageTracker._trackEvent(category, event, href);
  }
});</pre></div>
</div>


You can use the same method to unobtrusively add tracking code to file downloads:

<div class="CodeRay">
  <div class="code"><pre>var fileTypes = ['doc','xls','pdf','mp3'];

$('a').click(function() {
  var $a = $(this);
  var href = $a.attr('href');
  var hrefArray = href.split('.');
  var extension = hrefArray[hrefArray.length - 1];

  if ($.inArray(extension,fileTypes) != -1) {
      pageTracker._trackEvent('download', extension, href);
  }

});</pre></div>
</div>