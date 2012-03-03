---
layout: post
date: "2008-04-07"
title: Fix for slow-loading Google ads
comments: true
categories: howto, javascript, troubleshooting
---

Google's AdSense ads, and lots of others, are added to pages using Javascript, and if that Javascript appears early in the page's HTML, it can seriously slow down the rendering of the rest of the page. That's because browsers generally refuse to do any further rendering of the page until they have a requested Javascript file in hand.

We ran into this with the ad in the left column of some pages on <a href="http://dailystrength.org">DailyStrength.org</a>, which appears above the page content in the HTML; when we switched to a new ad provider, which required fetching multiple Javascript files, the issue was even more pronounced.

Since these ads only work when Javascript is enabled anyway, I decided to use some DOM manipulation (via jQuery, which is already on the page) to load the ad script in a hidden div at the bottom of the HTML, and then relocate the Javascript-generated iframe containing the ad to an empty, visible div where the ad needed to be:

{% codeblock lang:javascript %}
$('#ad_hide').find('iframe').appendTo('#ad');</pre></div>
{% endcodeblock %}

Now, loading the Javascript doesn't slow down the rendering of the content, and the ad appears right after the page is loaded.
