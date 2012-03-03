---
layout: post
date: "2008-05-19"
title: Another cautionary remote Javascript tale
comments: true
categories: front-end development, javascript
---

It sounds like TechCrunch got <a href="http://www.techcrunch.com/2008/05/17/dont-screw-your-partners-over-a-marketing-promotion/">burnt</a> yesterday by some remote Javascript appearing too high in their pages' HTML. Visitors' browsers were waiting and waiting for the remote script to load, and refusing to render the rest of the page until it did.
<blockquote class="posterous_medium_quote">All we knew is that our sites all simultaneously went down three times yesterday. After the first time we identified the likely problem as Seesmic and contacted the company. They assured us there was no way the plugin could take the site down. When it happened a second time we disabled the Seesmic plugin and the sites went back up. We identified the problem - the plugin was loading an external Javascript file, and when Seesmic’s servers were down, we just sat and waited for it for up to two minutes before timing out.</blockquote>
The remedy to this, of course, is to load remote scripts in the foot of the page, after all of the actual content has loaded. However, plenty of remote Javascript "widgets" aim at the least common denominator without offering a viable option for skilled users who don't actually want their site to break. The widget publishers simply require sites to put a remote script call exactly where the site wants the widget to appear. If the widget server chokes, the site is out of luck.

I wrote about a related problem with ad-related Javascript, and my attempts to get around its desire to appear wherever the ad appears, making page rendering grind to a halt if the ad network was slow. We ended up getting around that problem with a bit of utter absurdity: using an iframe that called some server-side code that would generate an HTML page that contained little more than a script tag.

Our solution, as it were, meant an extra HTTP request to our server for every ad on the page, just to avoid a problem that wouldn't exist in the first place if the authors of these widgets and ads would offer some real options to their savvier users. A simple method like

{% codeblock lang:javascript %}
widget.appendToElement('foo');
{% endcodeblock %}

seems like it would do the trick -- something that could be called at the end of a page and fail fairly silently by just leaving the destination element empty. I imagine that the use of such a method is hardly out of the reach of the good folks at TechCrunch, or plenty of other sites. Until an approach like this becomes a bit more common, expect many more rants like the one we saw from TechCrunch.
