--- 
layout: post
date: "2008-01-07"
title: Track outbound clicks with Google Analytics and jQuery
comments: true
categories: analytics, google analytics, howto, jquery, progressive enhancement, seo
---

<strong>Update:</strong> Make sure you check out the new <a href="http://code.google.com/apis/analytics/docs/eventTrackerOverview.html">_trackEvent</a> method of the Google Analytics pageTracker method. If your analytics account offers event tracking, this is the right way to do it if you don't want your events to count as pageviews. I've written a new post about <a href="http://blog.rebeccamurphey.com/2008/12/04/update-tracking-outbound-clicks-with-google-analytics-and-jquery/">tracking outbound clicks</a> using the new method.

--

Sometimes, you may want to know what outbound links a user is clicking on -- say, if you're linking to an affiliate's site and want to be able to count the clicks you're sending. <a href="http://www.google.com/support/googleanalytics/bin/answer.py?answer=55527&amp;topic=11006">Google Analytics offers this bit of code</a> to accomplish this:

<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>


and says:

<blockquote>Google Analytics provides an easy way to track clicks on links that lead away from your site. Because these links do not lead to a page on your site containing the UTM JavaScript, you will need to tag the link itself. This piece of JavaScript assigns a pageview to any click on a link - the pageview is attributed to the filename you specify.</blockquote>

Two problems here: one, it's Javascript in an onclick tag, which is ugly and bad; two, you've got to add that to every link you want to track. No fun.

Progressive enhancement says you should <strong>use Javascript to add behaviors that will only work with Javascript</strong>. I wanted to add this tracking ability to my blog, to see which outbound links people were clicking on. jQuery to the rescue:

<div class="CodeRay">
  <div class="code"><pre>$('a').each(function() {
  var $a = $(this);
  var href = $a.attr('href');

  // see if the link is external
  if ( (href.match(/^http/)) &amp;&amp; (! href.match(document.domain)) ) {

    // if so, add the GA tracking code
    $a.click(function() {
      pageTracker._trackPageview('/outgoing/' + href);
    });
  }

});</pre></div>
</div>


In my case, I wanted to track the post title (if there was one), in addition to the outgoing link's href. So:

<div class="CodeRay">
  <div class="code"><pre>$('a').each(function() {
  var $a = $(this);
  var href = $a.attr('href');

  if ( (href.match(/^http/)) &amp;&amp; (! href.match(document.domain)) ) {
    $a.click(function() {
      // get the post title if there is one
      // and add it to the string for tracking
      var postTitle = $a.parents('div.post').find('h2:first');
      href = href.replace('http://',''); // cleanup for nice GA reports
      href = (postTitle.length &gt; 0) ? postTitle.text() + '/' + href : href;

      pageTracker._trackPageview('/outgoing/' + href);
    });
  }

});</pre></div>
</div>


You can use the same method to unobtrusively add tracking code to file downloads:

<div class="CodeRay">
  <div class="code"><pre>var fileTypes = ['doc','xls','pdf','mp3'];

$('a').each(function() {
  var $a = $(this);
  var href = $a.attr('href');
  var hrefArray = href.split('.');
  var extension = hrefArray[href.length - 1];

  if ($.inArray(extension,fileTypes) != -1) {
    $a.click(function() {
      // get the post title if there is one
      // and add it to the string for tracking
      pageTracker._trackPageview('/documents/' + href);
    });
  }

});</pre></div>
</div>


See <a href="http://www.google.com/support/googleanalytics/bin/answer.py?answer=55527&amp;topic=11006">Google's help page</a> for more information. <em>Note that this example assumes you're using the new tracking code; you'll have to adjust accordingly if you're using the old tracking code.</em> If you're using the new tracking code, it will look like this:

<div class="CodeRay">
  <div class="code"><pre>var pageTracker = _gat._getTracker(&quot;UA-XXXXXX-X&quot;);
pageTracker._initData();
pageTracker._trackPageview();</pre></div>
</div>