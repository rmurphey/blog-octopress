---
layout: post
date: "2008-04-11"
title: Remote Javascript with document.write() is killing me
comments: true
categories: front-end development, javascript
---

I have been coming across way too much remote Javascript that uses document.write() to insert its contents. From ad providers to video hosting services, it's common practice to provide a Javascript tag that includes `document.write` and tell the site owner to just include it in their page where the ad or content should appear.

This is a great least-common-denominator approach for folks who don't want to be bothered with understanding how this stuff works. The problem is that when an ad or a video or any other content loaded via remote Javascript needs to appear near the top of the HTML document, before the content, the site user doesn't see any more content while the browser fetches the file. In the case of one service we tried, the initial script tag actually wrote <em>five more</em> remote script tags, each of which requires a separate HTTP request, amounting to a total delay of more than a second before the content appeared. In the meantime, the user was staring at a near-empty page.

There are <a href="http://wonko.com/post/remote_javascript_includes_without_the_performance_penalty_part">various</a> <a href="http://blog.rebeccamurphey.com/2008/04/06/fix-for-slow-loading-google-ads/">hacks</a> for getting around this, which involve loading the content low on the page and using (yet more) Javascript to move it to the appropriate place once the page has loaded, but they are hacky and not necessarily foolproof when it comes to ads. My initial experiments with doing this with some ad providers led to multiple ads appearing on the page, or to the whole page being replaced with just an ad.

If these services want people to stop complaining that their sites are loading slowly as a result of adding these tags (and perhaps looking at least for solutions that minimize the number of scripts that need to be loaded), it's time that they expose an API that will allow sites to include their <code>script</code> tag at the end of the page's HTML, <a href="http://developer.yahoo.com/performance/rules.html#js_bottom">where it belongs</a>. Then sites could call a method to insert the ad wherever it's needed, <em>once the content has finished loading</em>.

For example:

{% codeblock lang:javascript %}
ad.appendTo('#my_ad_container');
{% endcodeblock %}

I grant that this would require a bit more code from the ad providers (and that their ads would load a little bit later than they do now), but the improvement in user experience would be tremendous, relative to waiting as much as a second or two for the ad — and thus the rest of the page — to load.

--

Update: If you're looking for a fix to this problem, also check out <a href="http://ejohn.org/blog/xhtml-documentwrite-and-adsense/">John Resig's document.write() rewrite</a>.
