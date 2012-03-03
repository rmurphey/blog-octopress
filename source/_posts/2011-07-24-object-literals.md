---
layout: post
title: "Using object literals for flow control and settings"
---

I got an email the other day from someone reading through [jQuery Fundamentals](http://jqfundamentals.com) -- they'd come across the section
about patterns for performance and compression, which is based on a
presentation by [Paul Irish](http://paulirish.com) gave back at the 2009 jQuery
Conference in Boston.

In that section, there's a bit about alternative patterns for flow control --
that is, deciding what a program should do next. We're all familiar with the
standard if statement:

{% codeblock lang:javascript %}
function isAnimal(thing) {
  if (thing === 'dog' || thing === 'cat') {
    console.log("yes!");
  } else {
    console.log("no");
  }
}
{% endcodeblock %}

What stumped the person who emailed me, though, was when the same logic as we
see above was written like this:

{% codeblock lang:javascript %}
function isAnimal(thing) {
  if (({ cat : 1, dog : 1 })[ thing ]) {
    console.log("yes!");
  } else {
    console.log("no");
  }
}
{% endcodeblock %}

What's happening here is that we're using a throwaway object literal to express
the conditions under which we will say a `thing` is an animal. We could have
stored the object in a variable first:

{% codeblock lang:javascript %}
function isAnimal(thing) {
  var animals = {
    cat : 1,
    dog : 1
  };

  if (animals[ thing ]) {
    console.log("yes!");
  } else {
    console.log("no");
  }
}
{% endcodeblock %}

However, that variable's only purpose would be to provide this one lookup, so
it can be argued that the version that doesn't bother setting the variable is
more economical. Reasonable people can probably disagree about whether this
economy of bytes is a good tradeoff for readability -- something like this is
perfectly readable to a seasoned developer, but potentially puzzling otherwise
-- but it's an interesting example of how we can use literals in JavaScript
without bothering to store a value in a variable.

The pattern works with an array, too:

{% codeblock lang:javascript %}
function animalByIndex(index) {
  return [ 'cat', 'dog' ][ index ];
}
{% endcodeblock %}

It's also useful for looking up values generally, which is how I find myself
using it most often these days in my work with [Toura](http://toura.com), where
we routinely branch our code depending on the form factor of the device we're
targeting:

{% codeblock lang:javascript %}
function getBlingLevel(device) {
  return ({
    phone : 100,
    tablet : 200
  })[ device.type ];
}
{% endcodeblock %}

As an added benefit, constructs that use this pattern will return the
conveniently falsy `undefined` if you try to look up a value that doesn't have
a corresponding property in the object literal.

A great way to come across techniques like this is to read the source code of
your favorite library (and other libraries too). Unfortunately, once
discovered, these patterns can be difficult to decipher, even if you have
pretty good Google fu. Just in case your neighborhood blogger isn't available,
IRC is alive and well in 2011, and it's an excellent place to get access to
smart folks eager to take the time to explain.
