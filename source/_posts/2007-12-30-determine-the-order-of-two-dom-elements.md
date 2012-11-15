---
layout: post
date: "2007-12-30"
title: Determine the order of two DOM elements
comments: true
categories:
  - javascript
  - jquery
  - plugins
---

Inspired by <a href="http://us.php.net/usort">this</a> from PHP, I wanted a
utility function to determine whether a given element came before or after
another element in the DOM.

{% codeblock lang:javascript %}
(function($){
  $.order = function($a,$b) {

    $a = $a.eq(0);
    $b = $b.eq(0);
    var c = 'order-test';

    $a.addClass(c);

    if ($b.hasClass(c)) {
        // elements are the same
        return 0;
    }

    $b.addClass(c);

    var $elements = $('.'+c);
    $elements.eq(0).addClass(c+'-first');

    if ($a.hasClass(c+'-first')) {
        // $a is first
        return -1;
    } else if ($b.hasClass(c+'-first')) {
        // $b is first
        return 1;
    }

};

})(jQuery)
{% endcodeblock %}
