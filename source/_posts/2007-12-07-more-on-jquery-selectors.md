---
layout: post
date: "2007-12-07"
title: More on jQuery selectors
comments: true
categories:
  - javascript
  - jquery
  - selectors
---

Following up on my 13 seconds of selection hell:

<a href="http://benjaminsterling.com/jquery-what-are-the-fastest-selector/">jQuery: what are the fastest selectors?</a>

Turns out that, as you might expect, <code>$('div.class')</code> is faster than <code>$('.class')</code>, and so is <code>$('div').filter('.class')</code>. Knowing where <em>not</em> to look ("skip anything that's not a div") helps make jQuery faster. To which you might say, "well duh." Regardless, the post above does a nice job of demonstrating it pretty plainly, and it's a good reminder that, if I'd wanted to stick with my attribute-based selection, I could have helped my code a little bit by telling it where I expected to find those attributes so it wouldn't have had to look through every element on a ridiculously huge page.

{% codeblock lang:javascript %}
$('#data_entry input, #data_entry select').filter('[name=value]');</pre></div>
{% endcodeblock %}
