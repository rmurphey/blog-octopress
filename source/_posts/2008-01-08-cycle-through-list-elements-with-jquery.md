---
layout: post
date: "2008-01-08"
title: Cycle through list elements with jQuery
comments: true
categories: howto, jquery
---

I've written several versions of this functionality, and each time I revisit it, it gets simpler and I feel silly for how complicated I made it before. The thing to remember when you're working with jQuery is to <strong>always leverage the DOM</strong>. <a href="http://www.christianmontoya.com/2007/12/10/how-i-learned-to-stop-programming-and-love-the-dom/">Christian Montoya has a good writeup</a> about how he came to the same realization, and it's worth a read.

In the meantime, here's my latest iteration of cycling through items in an unordered list. In the example below, I've just put straight-up text in the list items, but they could contain entire <code>div</code>s of content if you wanted.

First, let's assume we have this HTML:

{% codeblock lang:html %}
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
</ul>
{% endcodeblock %}

We'll start by verifying that there's more than one item before we do all the rest of the work. (Of course, if your list length doesn't vary, this <code>if</code> isn't necessary.)

{% codeblock lang:javascript %}
var $list = $('#myList');

if ($list.length &gt; 1) {
  ...
}
{% endcodeblock %}


Following the principles of progressive enhancement, we don't want to include navigation elements in the HTML if they will only work with Javascript; rather, we want to add them with Javascript:

{% codeblock lang:javascript %}
var $list = $('#myList');

if ($list.length &gt; 1) {
  // put the nav before the list
  $list.before('');

  // style the nav as needed
  $('#nav').css({textAlign: 'right'});
  $('#nav img').css({cursor: 'pointer'});
  $('#nav img:first').attr('src','backButton.gif');
  $('#nav img:last').attr('src','forwardButton.gif');
}
{% endcodeblock %}


The comes the fun part: adding the behaviors to the nav buttons. This is where it pays to keep in mind the power of the DOM; in past iterations of this, I've seriously overthought the logic.

{% codeblock lang:javascript %}
var $list = $('#myList');

if ($list.length &gt; 1) {
  // put the nav before the list
  $list.before('');

  // style the nav as needed
  $('#nav').css({textAlign: 'right'});
  $('#nav img').css({cursor: 'pointer'});

  // add the back button behavior
  $('#nav img:first').
    attr('src','backButton.gif').
    click(function() {
      $('li:visible',$list).
        hide().
        prev('li').
          show();
    });

  // add the forward button behavior
  $('#nav img:last').
    attr('src','forwardButton.gif').
    click(function() {
      $('li:visible',$list).
        hide().
        next('li').
          show();
    });

}
{% endcodeblock %}


But what happens if there's not a next <code>li</code> when you click on the forward button, or a previous <code>li</code> when you click on the back button?

{% codeblock lang:javascript %}
var $list = $('#myList');

if ($list.length &gt; 1) {
  // put the nav before the list
  $list.before('');

  // style the nav as needed
  $('#nav').css({textAlign: 'right'});
  $('#nav img').css({cursor: 'pointer'});

  // add the back button behavior
  $('#nav img:first').
    attr('src','backButton.gif').
    click(function() {
      $('li:visible',$list).
        hide().
        prev('li').
          show();

      // if there wasn't a previous li,
      // show the last li in the list
      if ($('li:visible',$list).length
{% endcodeblock %}

One last thing: we need to hide all the list items and then show the first one:

{% codeblock lang:javascript %}
var $list = $('#myList');

if ($list.length &gt; 1) {

  // hide all the list items and show the first one
  $('li',$list).hide().eq(0).show();

  // put the nav before the list
  $list.before('');

  // style the nav as needed
  $('#nav').css({textAlign: 'right'});
  $('#nav img').css({cursor: 'pointer'});

  // add the back button behavior
  $('#nav img:first').
    attr('src','backButton.gif').
    click(function() {
      $('li:visible',$list).
        hide().
        prev('li').
          show();

      // if there wasn't a previous li,
      // show the last li in the list
      if ($('li:visible',$list).length
{% endcodeblock %}

For extra fun:

<ul>
<li>
<a href="http://blog.rebeccamurphey.com/2007/12/11/jquery-plugin-randomly-reorder-children-elements/">Randomly reorder</a> the list items before you add the rest of the code</li>
<li>Instead of <code>show()</code> and <code>hide()</code>, try <code>fadeIn()</code> and <code>fadeOut()</code>
</li>
</ul>
