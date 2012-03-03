---
layout: post
date: "2008-01-13"
title: Fix uneven line lengths in headlines
comments: true
categories: howto, jquery
---

I used to work in newspapers, and we were diligent about fixing "bad breaks" in headlines. Nothing looks worse than a headline that's far longer on one line than on another. Desktop publishing software has some intelligence about these bad breaks, but the web, not so much. A little jQuery can clean up the bad breaks, though -- for example, this will clean up all the <code>h2</code>s.

{% codeblock lang:javascript %}
$('h2').each(function() {
  $h2 = $(this);

  // get the height of the existing element
  var height = $h2.height();

  // get the text of the existing element
  var text = $h2.text();

  // put temporary text in the element
  // that will make the element one line tall
  $h2.text('temp');

  // get the height of the one-line element
  var singleLineHeight = $h2.height();

  // check to see if the initial element was
  // taller than the one-line element;
  // if so, look for the midpoint of the
  // initial text and split it there
  if (height &gt; singleLineHeight) {
    var length = text.length;
    var mid = parseInt(length/2);
    for (i=mid; i&gt;0; i--) {
      if (text[i] == ' ') {
        var breakIndex = i;
        break;
      }
    }
    var newText = '';
    for (i=0; i';
      } else {
        newText = newText + text[i];
      }
    }
    $h2.html(newText);

  } else {

    // otherwise, put the initial text
    // back in the element and move on
    $h2.text(text);

  }
});
{% endcodeblock %}


So you end up changing this:

{% codeblock %}
This is a very long headline that goes on and then breaks
badly
{% endcodeblock %}

to something like this:

{% codeblock %}
This is a very long headline that
goes on and then breaks badly
{% endcodeblock %}

You'll want to change the <code>$('h2')</code> selector to exactly what you need; in the case of this blog, <code>$('div.post h2 a')</code> is actually more appropriate. You're going to be best off if matching elements contain only text, and no HTML.

Also, this will only work for elements that are one or two lines long; for headlines of arbitrary length, you'd need to see exactly how the height of the initial element compared to the height of the temporary one-line element, and then split the headline multiple times. Then again, thinking back to my newspaper days where we had to convey a whole story in four words, you might ask yourself whether you actually need a headline that's more than two lines long.
