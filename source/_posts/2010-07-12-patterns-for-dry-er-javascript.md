---
layout: post
date: "2010-07-12"
title: Patterns for DRY-er JavaScript
comments: true
categories: front-end development, howto, javascript
---

<p>I came across a little code the other day that reminded me I've been meaning to write about JavaScript patterns I take for granted. The code in question was intended to set the value of some fields in a form when a checkbox was selected; when it was deselected, the same fields were to be emptied. It looked not unlike this:</p>

{% codeblock lang:javascript %}
// config is defined outside of this snippet,
// and may contain more than the properties
// we care about 
$('#myCheckbox').click(function() {
  if (this.checked) {
    $('#field_foo').val(config.foo);
    $('#field_bar').val(config.bar);
    $('#field_baz').val(config.baz);
  } else {
    $('#field_foo').val('');
    $('#field_bar').val('');
    $('#field_baz').val('');
  }
});
{% endcodeblock %}

<p>This is a wholly readable bit of code -- there's almost no question what's going on here. On the other hand, it's pretty easy to see the rampant repetition; this code isn't interested in "don't repeat yourself" (DRY). We're calling the same method on every selection we make, and our selections are repeated in both the if and else block.   When I saw this code, I had an immediate inclination to rewrite it. Here's what I came up with first:</p>

{% codeblock lang:javascript %}
// config is defined outside of this snippet,
// and may contain more than the properties
// we care about

$('#myCheckbox').click(function() {
  // note whether the checkbox is checked
  var checked = this.checked;

  // iterate over the keys we care about
  $.each(['foo', 'bar', 'baz'], function(i,v) {
    // find the field for the given key
    $('#field_' + v)
      // and set its value either to the string
      // stored for the key, or to an empty string,
      // depending on whether the checkbox was checked
      .val(checked ? config[v] : '');
  });
});
{% endcodeblock %}

<p>This looks approximately nothing like the initial code, and without the
comments, the code itself would be substantially less readable than the
original. The idealistic part of me -- the part that believes people who write
JavaScript should understand JavaScript -- says this is an acceptable price to
pay. And besides, there's something to be said for explaining the code in a
comment that can be stripped by a minifier, rather than explaining the code via
the code.  In this iteration, we've introduced two patterns for DRY-er code:
iterating over an array literal (or, alternately, an object) to achieve
repetition without repeating ourselves, and using the ternary operator in place
of an if/else statement when the simplicity of our logic allows it.  The array
literal serves as a list of the fields we care about. When our checkbox is
clicked, we iterate over this list, build up a selector for each item in the
list, make our selection, and then set the field value using a <a
href="http://stackoverflow.com/questions/1788917/javascript-ternary-operator">ternary
operator</a>. We've gone from 11 lines of code to six, with the added bonus
that we have to do a lot less typing if we need our checkbox to affect more
fields.

(A side note: Is this premature optimization? I'd argue no, if you've
learn to see these patterns before you start writing code. Once you learn how
to spot these patterns in a requirement, writing code that embraces them can
actually be easier than writing code that takes a more "literal" approach to
the problem. For example, imagine if the checkbox affected 20 other fields
instead of one? You'd undoubtedly find yourself copying and pasting code if you
took the more "literal" approach to the problem, and that would be your first
clue that you were doing something inefficiently.)

The great thing about using a pattern like this is that it rapidly exposes the
actual meat of what you're doing, and makes refactoring far less painful. I
also find that it helps me see opportunities for reuse that I might not have
spotted in the more literal version of the code.  Let's say we're feeling all
proud of ourselves for DRYing out our code using clever JavaScript that only
super-smart people can read. Now there's another checkbox that needs similar
behavior, but it's going to use a different config object and a different list
of fields. No problem! You've already written this code, so you can just copy
and paste it and then change what's different. Sweet. Er ... suddenly you're
not looking so DRY after all.  This is when another pattern comes into play:
creating a function that returns another function with certain variables
already baked in (that is, creating a closure). We'll execute this creator
function and then use the function it returns in place of the anonymous
function we were using previously when we bound to the click event.</p>

{% codeblock lang:javascript %}
// handleClick accepts a config object
// and a makeSelector function; it returns
// a function that can be bound to
// a click event, using the config object
// and the makeSelector function to react
// appropriately to the click
var handleClick = function(fields, config, makeSelector) {
  return function() {
    var checked = this.checked;

    fields && $.each(fields, function(i, v) {
      // build the selector using the provided
      // makeSelector function
      $(makeSelector(v))
              // set the value using the
              // config object, depending
              // on whether the checkbox
              // is checked
              .val(checked ? config[v]: '');
    });
  };
};

$('#myCheckbox').click(
  // use handleClick to create a function
  // that has these variables baked in;
  // pass the created function as the
  // click handling function
  handleClick(
    ['foo','bar','baz'],
    myCheckboxConfig,
    function(field) { return '#field_' + field; }
  )
);

$('#myOtherCheckbox').click(
  handleClick(
    ['bim','bar','bop'],
    myOtherCheckboxConfig,
    function(field) { return 'input[name=&quot;' + field + '&quot;]'; }
  )
);
{% endcodeblock %}

<p>By creating a function that returns a function, we can isolate what's different about the two different handlers while centralizing the pieces that are the same. If your event handling function was slightly less trivial than this one, or if you were binding to five different checkboxes instead of two, the benefit of consolidating the code would be even more substantial.  JavaScript offers plenty of patterns for writing DRY-er code; it's important to learn to both recognize and use them. It's also important to recognize when you're writing un-DRY code in the first place -- copying and pasting code is one crystal-clear indicator, but others are more subtle and you may not identify them on the first go-round. For example, take these two functions; each receives a list item as its only argument and returns either the next or previous list item, returning to the beginning or end of the list if there is no next or previous item.</p>

{% codeblock lang:javascript %}
var getNextItem = function($item) {
  return $item.next().length ?
         $item.next() : $items.first();
};

var getPrevItem = function($item) {
        return $item.prev().length ?
                $item.prev() : $items.last();
};
{% endcodeblock %}

<p>This felt repetitive when I first wrote it, but I couldn't quickly come up with a single function that would work. A little thinking about it, though, led me to this single function which gets a second argument: direction. That argument is used to decide whether to run the item's next or previous method, and then whether to run the item's first or last method. Besides combining two functions into one, it also eliminates calling next or prev twice inside each function.</p>

{% codeblock lang:javascript %}
var getItem = function($item, direction) {
  var $returnItem = $item[direction]();
  return $returnItem.length ?
         $returnItem :
         $items[(direction == 'next') ? 'first' : 'last']();
};
{% endcodeblock %}

<p>Learning about patterns and then discovering opportunities to use them is one of the more pleasing parts of my job. I hope this helps you identify some of those opportunities for yourself :)</p>
