---
layout: post
title: "Objects as Arguments: Where do you draw the line?"
date: 2011-04-25 11:16
comments: false
categories: javascript
---

I was reviewing some code last week and came across a snippet that looked a lot like this:

{% codeblock lang:javascript %}
var someObject = {
  // ...

  onSuccess : function(resp) {
    // ...
    this.someMethod(resp.token, resp.host, resp.key, resp.secret);
  },

  someMethod : function(token, host, key, secret) {
    // ...
  }
};
{% endcodeblock %}

My immediate response was to suggest that it didn't make sense to be passing
four separate arguments to `someMethod`, especially when the arguments were
being "unpacked" from an already-existing object. Certainly we could just pass
the `resp` object directly to `someMethod`, and let `someMethod` unpack it as
necessary -- we'd save some bytes, and we'd also leave ourselves some room to
grow. "I'm not a big fan of functions that take four arguments," I said in my
GitHub comment.

To the original author's credit, "because I say so" wasn't sufficient reason to
rewrite code that was working just fine, thank you very much. If four arguments
was too many, was two arguments too many? Why draw the line at four? Surely the
four-argument signature helped indicate to future developers what was required
in order for the function to ... function. Right? My hackles momentarily
raised, I parried by pointing out that if the arguments were actually required
by the function, maybe the function ought to actually check for their presence
before using them. Ha! While the original author was distracted by my disarming
logic, I fretted over the fact that I use a function that take four arguments
every day: `dojo.connect(node, 'click', contextObj, 'handlerMethod')`. Ohnoes.

So where do you draw the line? Certainly you could write that `dojo.connect`
call like so:

{% codeblock lang:javascript %}
dojo.connect({
  node : node,
  event : 'click',
  context : contextObj,
  method : 'handlerMethod'
});
{% endcodeblock %}

This, though, might make you poke your eyes out. It certainly isn't as concise
as the four-argument approach, and it makes a lot of things like
[partial](http://ejohn.org/blog/partial-functions-in-javascript/)
[application](http://dojotoolkit.org/reference-guide/dojo/partial.html#dojo-partial)
a lot harder. Clearly there's more to this than "if there's more than four
arguments, put them in an object" ... but what are the rules?

## Optional Arguments

Probably the most compelling reason to use an object is when there are several
optional arguments. For example, last fall I was reviewing some code from a
potential training client, and I came across this:

{% codeblock lang:javascript %}
addBling('#awesome', 'fuchsia', 'slow', null, null, 3, 'done!');
{% endcodeblock %}

No one can argue that this is not terrible, and yet every experienced
JavaScript developer knows how the developer(s) who wrote it arrived there. At
first, the function needed three arguments, and all was good with the world.
But then, it seemed like the same function could be used to do another thing by
just passing two more arguments -- no big deal, because if those two arguments
weren't present, then just the first three would suffice. Five arguments
certainly isn't *that* bad, right? After that, though, things went south: for
whatever undoubtedly marketing-department-driven reason, suddenly both the
original three-argument case and the later five-argument case both needed to
receive two more arguments, and these two new arguments were mandatory. Now
both cases had seven-argument signatures, and in some cases, two of those seven
arguments needed to be null so nothing would break.

This case demonstrates the most compelling reason to switch to using an object
instead: optional arguments. When the developer discovered that the original,
three-argument `addBling` could be used for the five-argument case as well, it
was probably time to refactor:

{% codeblock lang:javascript %}
// original
addBling('#awesome', 'fuchsia', 'slow');

// new hotness
addBling('#awesome', {
  color : 'fuchsia',
  speed : 'slow'
});
{% endcodeblock %}

Then, the same function could be used while passing it more information about
how to behave in the five-argument case:

{% codeblock lang:javascript %}
addBling('#omgSoAwesome', {
  color : 'fuchsia',
  speed : 'slow',
  unicorns : 3,
  rainbows : 5
});
{% endcodeblock %}

Then, when it came time to add yet more bling, the function signature wouldn't need to change,

{% codeblock lang:javascript %}
addBling('#awesome', {
  color : 'fuchsia',
  speed : 'slow',
  timesToBlink : 3,
  alertOnSuccess : 'done!'
});

addBling('#omgSoAwesome', {
  color : 'purple',
  speed : 'fast',
  unicorns : 3,
  rainbows : 5,
  timesToBlink : 9001,
  alertOnSuccess : 'woohoo!'
});
{% endcodeblock %}

## Extensibility and Future-Proofing

Another case for passing in an object is when you want the flexibility that an
object provides, even if your code doesn't require it for now:

{% codeblock lang:javascript %}
var Person = function(args) {
  this.firstName = args.firstName;
  this.lastName = args.lastName;
  return this;
};
{% endcodeblock %}

For now, you only want to be able to provide the first and last name of the
person -- it would work just fine to create a function signature for the
`Person` constructor that took exactly those two arguments, because indeed they
are required. On the other hand, though, this is incredibly short-sighted --
while first and last name may be all that you care about now, there's obviously
more to a person than those two attributes, and eventually you may want to
provide attributes such as age, occupation, etc. Doing this with individual
arguments quickly becomes unsustainable. Besides that, though, it also makes
assigning instance properties a pain in the ass. By passing an object, we can
rewrite the above code as such:

{% codeblock lang:javascript %}
var Person = function(args) {
  dojo.mixin(this, args);
  // jQuery: $.extend(this, args);
  return this;
};
{% endcodeblock %}

Now -- assuming this is what we want -- we can mix in *any* settings we provide
in the `args` argument. Dojo, for example, bakes this ability in to anything
that inherits from `dijit._Widget`:

{% codeblock lang:javascript %}
var thinger = new my.Thinger({
  title : 'Favorite Animals',
  animals : [ 'Narwhal', 'Lemur', 'Honey Badger' ]
});
{% endcodeblock %}

## Use Objects for Related Data

An important qualifier here is that all of the properties of an object that
we've talked about passing to our `Person` constructor are related -- they all
are saying something about the Person you're creating. What if creating our
Person was asynchronous, and we wanted to run a function once our Person was
created? In a (contrived) case like that, I think it does make sense to pass in
a separate argument:

{% codeblock lang:javascript %}
new Person(configObj, fn);
{% endcodeblock %}

In this particular example, we still only have two arguments -- we haven't
wandered into that muddy realm of four or more. That said, I think this
distinction is part of what makes `dojo.connect(node, 'click', contextObj,
'handlerMethod')` OK: the arguments are four distinctly different types of
information. Taken together, they have an almost narrative quality: when this
node receives a click, use the context object's `handlerMethod`. A signature
like `new Person('Rebecca', 'Murphey', 34, 'web developer', 2 /*cats*/, 2
/*dogs*/)` doesn't *feel* the same as the `dojo.connect` example -- it's
information that's too related to be expressed as independent arguments.

## Four or More, Time to Refactor?

I think the bottom line here is a) it's complicated, and b) if your function
signature has four or more arguments, you should almost certainly consider
whether there's a better way to do it. If the arguments are super-related, it
may be they belong in an object, so you get the benefit of easy extensibility
down the road. If there are optional arguments, you almost certainly want to
wrap those in an object to avoid passing `null` over and over again.

Personally, my threshold is actually closer to two arguments -- if I find
myself wanting a third argument, I question whether my function is trying to do
more than it should be doing -- maybe I should do some pre-processing of the
input so I can get away with just passing in two arguments. Every additional
argument is an indication of additional complexity, which means an additional
opportunity for things to go wrong.

## Other Considerations

I posed this question to
[Twitter](http://twitter.com/#!/rmurphey/status/62172041037623296) and got a
ton of interesting feedback. Here are some of the highlights that I didn't
cover above:

- [@raydaly](https://twitter.com/raydaly/status/62320791232069632) no new nouns
  is my principle. If unrelated data needs to be passed, diff args.
- [@dadaxl](https://twitter.com/dadaxl/status/62308649699250176) I would pass
  an obj if I've a dynamic bunch of args containing functions.
- [@sh1mmer](https://twitter.com/sh1mmer/status/62308597153021952) omg! Objects
  for the love of god! No one likes immutable APIs. Just ask @ls\_n
- [@MattRogish](https://twitter.com/mattrogish/status/62307288597266432) Rails
  tends to do required things are named args, optional things are a hash
- [@ryanflorence](https://twitter.com/ryanflorence/status/62511600837468161)
  obfuscation often influences me, objects don't compress as well as multiple
  args.
- [@getify](https://twitter.com/getify/status/62512606132449280) if more than
  half of the args are optional...or if there are several boolean params which
  without names can be confusing
- [@jcoglan](https://twitter.com/jcoglan/status/62253184369954816) When further
  args are optional, or args.length&gt;3. Need to spot when options merit a
  refactoring, though.
- [@digitalicarus](https://twitter.com/digitalicarus/status/62247330354692096)
  A combo of sheer length, amount of reuse, if it's an API, and/or if it's
  designed to be called a variety of ways to a similar end.
- [@BryanForbes](https://twitter.com/bryanforbes/status/62237053148086272) If I
  have to start swapping arguments and type checking, it's time for one object
  or reworking my function.
- [@myfreeweb](https://twitter.com/myfreeweb/status/62229278581993472) I use an
  object when I start forgetting the order of args ... or there is no logical
  order like (key, value, callback) at all
- [@zetafleet](https://twitter.com/zetafleet/status/62209835097268224) When
  many of the arguments are optional or they&rsquo;re all getting stored or
  copied directly over to the object.
- [@maravillas](https://twitter.com/maravillas/status/62176422877335552) I
  usually don't make an obj just for passing data; if arglist is too long,
  maybe the function does too much and needs refactoring.

## Postscript

We ended up leaving the code that spurred this whole conversation exactly as it
was.
