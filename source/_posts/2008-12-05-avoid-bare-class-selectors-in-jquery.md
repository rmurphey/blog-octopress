---
layout: post
date: "2008-12-05"
title: Avoid bare class selectors in jQuery
comments: true
categories: jquery, optimization, selectors
---

It just happened again: I was looking at someone else's jQuery and I came across something like this:

{% codeblock lang:javascript %}
$('.button').click(function() { /* do something */ });
{% endcodeblock %}

This is a classic case of "just because you can, doesn't mean you should." This little bit of jQuery will, indeed, find every element on a page with a class of "button", but that's exactly the problem: it has to look at every element on the page to figure out which ones match the selector. It seems short and sweet, like so much of jQuery is, but on a page with a lot of elements, this selector can actually take a non-trivial amount of time to run.

There are a few ways to avoid this:

<ul>
<li>If the element you're after has an ID attribute, use it. That's the single-fastest way to find an element. However, don't gratuitously add ID attributes to elements; the other methods below are perfectly good.</li>
<li>Specify the type of element you're after. For example, <code>$('input.button')</code>. This will tell jQuery that it's only looking for inputs, so it can disregard anything on the page that isn't an input. If you're looking for multiple element types, tell jQuery that: <code>$('input.button, a.button')</code>
</li>
<li>Give jQuery some information about where to look for the element. For example: <code>$('#myForm .button')</code>
</li>
<li>Use an element you've already found to tell jQuery where to find the element:

{% codeblock lang:javascript %}
var $ul = $('#myUnorderedList')
var $li = $ul.find('.selected');
{% endcodeblock %}

</li>
</ul>
