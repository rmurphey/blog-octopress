---
layout: post
date: "2010-09-15"
title: "In Search of JavaScript Developers: A Gist"
comments: true
categories: javascript, performance, refactoring
---

<p>I posted a <a href="http://gist.github.com/576723">gist</a> the other day in an attempt to locate some JavaScript help. I&rsquo;m finding, lately, that I&rsquo;m being asked to do more work than I can comfortably take on without having some overflow capacity, but I&rsquo;ve been a little lax at actually identifying people who can provide that capacity up until now. That meant I was turning down work, and that&rsquo;s not a good thing.</p>

<p>I had a great time the last couple of days watching people come up with solutions, and was especially gratified that so many people who weren&rsquo;t looking for work thought that answering JavaScript questions sounded like fun.</p>

<p>A lot of people have asked if I&rsquo;d be providing &ldquo;answers&rdquo; to the questions I posed in the gist, so I wanted to try to do that, mixed in with a bit of commentary about what I was looking for.</p>

<h2>Question 1: Writing Readable but Terse Code</h2>

{% codeblock lang:javascript %}
// 1: how could you rewrite the following to make it shorter?
if (foo) {
  bar.doSomething(el);
} else {
  bar.doSomethingElse(el);
}
{% endcodeblock %}


<p>Answers to this question told me whether to keep reading; I was looking for something like:</p>

{% codeblock lang:javascript %}
bar[foo ? 'doSomething' : 'doSomethingElse'](el);

// OR

bar['doSomething' + (foo ? '' : 'Else')](el);
{% endcodeblock %}

<p>To be honest, the second one is maybe a little too clever. But generally, I wanted to see submissions that understood using the ternary operator instead of an if statement to create shorter but still-readable code when all that is different is a method name.</p>

<p>Some people submitted an answer that used a ternary operator, but didn&rsquo;t take advantage of it to just switch the method name:</p>

{% codeblock lang:javascript %}
foo ? bar.doSomething(el) : bar.doSomethingElse(el);
{% endcodeblock %}


<p>This is an improvement, definitely, but there&rsquo;s room for more.</p>

<h2>Question 2: Understanding Variable Scope</h2>

{% codeblock lang:javascript %}
var foo = 'hello';

(function() {
  var foo = foo || 'world';
  console.log(foo);
})();
{% endcodeblock %}


<p>This was a question where, in hindsight, what I was looking for may not have been super-clear. I got a lot of right-enough answers, but really what I wanted to hear was that the <code>||</code> statement was absurd, because <code>foo</code> would always evaluate to <code>'world'</code> due to <a href="http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting">variable hoisting</a>.</p>

<p>This can be sort of a sort of crazy thing to wrap your head around, but basically JavaScript scans the contents of a function for <code>var</code> statements <em>before</em> it runs the function. Any variable initialized with a <code>var</code> statement inside the function will become local to the function, <em>even if the variable is &ldquo;used&rdquo; before it is initialized</em>. Changing the order of the two lines inside the function shows this readily:</p>

{% codeblock lang:javascript %}
var foo = 'hello';

(function() {
  console.log(foo); // undefined!
  var foo = foo || 'world';
})();
{% endcodeblock %}


<p>Some submissions thought I wanted access to the external <code>foo</code> inside the closure &mdash; not an unreasonable interpretation of the question.</p>

{% codeblock lang:javascript %}
var foo = 'hello';

(function(f) {
  var foo = f || 'world';
  console.log(foo);
})(foo);
{% endcodeblock %}


<p>Anyway, there were lots of right-enough answers, but anyone who talked about hoisting definitely caught my eye.</p>

<h2>Question 3: Working with Objects and Prototypes</h2>

<p>This question was looking for really basic understanding of prototypes. It also was written to be a tad hard to follow, lumping all the questions into a single paragraph, to simulate a not-atypical client request.</p>

{% codeblock lang:javascript %}
// 3: given the following code, how would you override the value of the
// bar property for the variable foo without affecting the value of the
// bar property for the variable bim? how would you affect the value of
// the bar property for both foo and bim? how would you add a method to
// foo and bim to console.log the value of each object's bar property? how
// would you tell if the object's bar property had been overridden for the
// particular object?
var Thinger = function() {
  return this;
};

Thinger.prototype = {
  bar : 'baz'
};

var foo = new Thinger(),
    bim = new Thinger();
{% endcodeblock %}


<p>The good submissions broke the question down into separate comments, and then showed the answers:</p>

{% codeblock lang:javascript %}
// override the bar prop for foo w/o affecting bim
foo.bar = 'new value';

// change the bar prop for both foo and bim
// (*if* it hasn't been overridden locally!)
Thinger.prototype.bar = 'another new value';

// we could delete foo.bar now and it would get
// the prototype value instead
// delete foo.bar;

// add a method to foo and bim to log bar
Thinger.prototype.logger = function() {
  console.log(this.bar);
};

// check if bar has been overridden
foo.hasOwnProperty('bar'); // true
bim.hasOwnProperty('bar'); // false
{% endcodeblock %}

<h2>Question 4: Iterating over Objects</h2>

<p>This one was pretty basic:</p>

{% codeblock lang:javascript %}
// 4: given the following code, and assuming that each defined object has
// a 'destroy' method, how would you destroy all of the objects contained
// in the myObjects object?
var myObjects = {
  thinger : new myApp.Thinger(),
  gizmo : new myApp.Gizmo(),
  widget : new myApp.Widget()
};
{% endcodeblock %}


<p>Really I just wanted to see people iterate over an object without the use of a helper like <code>jQuery.each</code>. The <code>hasOwnProperty</code> check may seem like overkill, but I was glad when people didn&rsquo;t leave it out. Adding in the delete statement was another nice touch, though not strictly required by the question.</p>

{% codeblock lang:javascript %}
for (var obj in myObjects) {
  if (myObjects.hasOwnProperty(obj)) {
    myObjects[obj].destroy();
    delete myObjects[obj];
  }
}
{% endcodeblock %}


<h2>Question 5: Solving Deceptively Simple Problems</h2>

<p>This question was probably the most fun, because even though it was a dead-simple task, the answers were all over the map. This was the question:</p>

{% codeblock lang:javascript %}
// 5: given the following array, create an array that contains the
// contents of each array item repeated three times, with a space between
// each item. so, for example, if an array item is 'foo' then the new
// array should contain an array item 'foo foo foo'. (you can assume the
// library of your choice is available)
var myArray = [ 'foo', 'bar', 'baz' ];
{% endcodeblock %}

<p>Rather than going through the different answers one at a time, I&rsquo;m just going to tell you to visit this <a href="http://jsperf.com/rmurphey-foo-foo-foo/9">JSPerf test page</a> to see some of the variations, and their relative performance.</p>

<p>This brings up a good question <a href="http://twitter.com/ryanflorence/status/24505624622">tweeted by Ryan Florence</a>:</p>

<blockquote><p>T | F &ndash; In #JavaScript, 90% of the time we do stuff only a few times, maybe hundreds. Therefore, 90% of the time Readability > Performance.</p></blockquote>

<p>I tend to come down on the side of readability and compression over straight-up perf, precisely because we&rsquo;re rarely doing anything that&rsquo;s actually that intensive. On the other hand, we should avoid doing things that are outright stupid; where the line gets drawn depends a lot, I think, on experience.</p>

<p>Here&rsquo;s the thing, though: Something that seems like a gratuitous and obscure optimization to a less experienced developer might seem completely readable and obvious to a more experienced developer. How to balance this? Can comments bridge the gap? Should the gap be bridged? I dunno.</p>

<h2>Question 6: Basic jQuery Best Practices and DRY</h2>

<p>I see way too much code in real life that looks like this question.</p>

{% codeblock lang:javascript %}
// 6: how could you improve the following code?
$(document).ready(function() {
  $('.foo #bar').css('color', 'red');
  $('.foo #bar').css('border', '1px solid blue');
  $('.foo #bar').text('new text!');
  $('.foo #bar').click(function() {
    $(this).attr('title', 'new title');
    $(this).width('100px');
  });

  $('.foo #bar').click();
});
{% endcodeblock %}


<p>There are a slew of things wrong in this tiny snippet. First and foremost, making the same selection repeatedly suggests that the author fundamentally doesn&rsquo;t understand what their code is doing, or the expense they&rsquo;re incurring in doing it; the selection should be made once, and then the selection should be cached and/or the methods should be chained.</p>

<p>While it wasn&rsquo;t imperative, submitters did well to point out that CSS changes should be made via class names instead of hard-coded CSS in JavaScript; they also did well to put the click handler in a named function. Finally, while there may be cases where an ID selector needs to be prefixed by a class, i.e. <code>.foo #bar</code>, I appreciated it if people questioned this.</p>

{% codeblock lang:javascript %}
$(document).ready(function() {
  var handleClick = function(el) {
        el.attr('title', 'new title')
          .width('100px');
      },

      bar = $('#bar')
        // ideally: use a class for this
        .css({
          color : 'red',
          border : '1px solid blue'
        })
        .text('new text!')
        .click(function(e) {
          handleClick($(e.target));
        });

  handleClick(bar);
});
{% endcodeblock %}


<h2>Question 7: Asynchronicity</h2>

<p>This is a pretty newby thing, but I wanted to make sure people understood the basic concept of async requests &mdash; that is, you can&rsquo;t set the value of a variable inside an XHR&rsquo;s callback and expect that value to be available immediately.</p>

{% codeblock lang:javascript %}
(function() {
  var foo;

  dojo.xhrGet({
    url : 'foo.php',
    load : function(resp) {
      foo = resp.foo;
    }
  });

  if (foo) {
    // run this important code
  }
})();
{% endcodeblock %}


<p>Fixing this just involves waiting for the XHR to complete before running the code that expects <code>foo</code> to be set. (Alternately, you could make the request run synchronously by setting <code>sync : true</code> in the XHR config object.)</p>

<p>There was one other issue with this code as well: Dojo needs to know the response should be handled as JSON, else it will handle it as text. If a submitter missed this, I didn&rsquo;t hold it against them &mdash; Pete Higgins actually had to point it out to me :) That said, it would become pretty obvious pretty quickly in real code.</p>

{% codeblock lang:javascript %}
(function() {
  dojo.xhrGet({
    url : 'foo.php',
    handleAs : 'json'
  })
  .addCallback(function(resp) {
    if (resp && resp.foo) {
      // do stuff
    }
  });
})();
{% endcodeblock %}

<p>Note that the callback function could also be specified in the XHR config object using the <code>load</code> property; Dojo&rsquo;s XHRs are great in that while you can specify everything in a config object, you can also attach callbacks to the return value of the XHR methods. You should <a href="http://docs.dojocampus.org/dojo/Deferred">read more about this</a> because it is very pleasant.</p>

<h2>Question 8: DRY</h2>

<p>Repetitive code is dumb.</p>

{% codeblock lang:javascript %}
// 8: how could you rewrite the following code to make it shorter?
(function(d, $){
  $('li.foo a').attr('title', 'i am foo');
  $('li.bar a').attr('title', 'i am bar');
  $('li.baz a').attr('title', 'i am baz');
  $('li.bop a').attr('title', 'i am bop');
})(dojo, dojo.query);
{% endcodeblock %}

<p>How far you want to go with DRYing this out is debatable, but to me this cries out for improvement. Here&rsquo;s what I&rsquo;d do:</p>

{% codeblock lang:javascript %}
(function(d, $){
  d.forEach(['foo', 'bar', 'baz', 'bop'], function(c) {
    $('li.' + c + ' a').attr('title', 'i am ' + c);
  });
})(dojo, dojo.query);
{% endcodeblock %}

<p>I&rsquo;d be lying if I didn&rsquo;t mention that I also wanted to show here how easy it is to make Dojo look like jQuery. After all, it&rsquo;s just JavaScript, right?</p>

<h2>Question 9: DOM Manipulation Best Practices &amp; DRY</h2>

<p>I thought it was well-known that we don&rsquo;t append 202 things to the DOM one at a time; the good news is, most people did know this. The bad news is, some people did not.</p>

<p>In addition to doing 202 appends, this code also does 202 selections. To top it off, the iterator <code>i</code> is global because we didn&rsquo;t prefix it with <code>var</code>.</p>

{% codeblock lang:javascript %}
// 9: how would you improve the following code?
for (i = 0; i <= 100; i++) {
  $('#thinger').append(
    '<p><span class="thinger">i am thinger ' + i + '</span></p>'
  );
  $('#gizmo').append(
    '<p><span class="gizmo">i am gizmo ' + i + '</span></p>'
  );
}
{% endcodeblock %}


<p>Here&rsquo;s a fix:</p>

{% codeblock lang:javascript %}
var thingerDom = [], gizmoDom = [],
    tpl = '<p><span class="%s">i am %s %i</span></p>',
    tplFn = function(str, i) {
      return tpl.replace(/%s/g, str).replace(/%i/g, i);
    },
    i;

for (i = 0; i <= 100; i++) {
  thingerDom.push(tplFn('thinger', i));
  gizmoDom.push(tplFn('gizmo', i));
}

$('#thinger').append(thingerDom.join(''));
$('#gizmo').append(gizmoDom.join(''));
{% endcodeblock %}

<p>There&rsquo;s more that could be done here to DRY this out a bit more, but the fix addresses the main problem of excessive DOM manipulation.</p>

<h2>Question 10: Loose Typing</h2>

<p>Numbers in JavaScript suck, especially when the user enters them.</p>

{% codeblock lang:javascript %}
// 10: a user enters their desired tip into a text box; the baseTotal,
// tax, and fee values are provided by the application. what are some
// potential issues with the following function for calculating the total?
function calculateTotal(baseTotal, tip, tax, fee) {
  return baseTotal + tip + tax + fee;
}
{% endcodeblock %}


<p>How you&rsquo;d actually deal with this problem would probably depend on the business logic of your application; you may be well-advised to convert everything to integers instead of trying to deal with decimals, because math with floats in JavaScript can have issues.</p>

<p>What I wanted to see in submissions, though, was an awareness that the tip would come to us as a string, and we couldn&rsquo;t just add it to the other arguments and expect a useful result. I was more interested in the discussion of this problem, and other problems that could arise, but here&rsquo;s at least the beginning of a solution:</p>

{% codeblock lang:javascript %}
function calculateTotal(baseTotal, tip, tax, fee) {
  // convert the tip to a number using base 10;
  // allow for a NaN result from parseFloat
  tip = parseFloat(tip) || 0;

  // don't allow a negative tip
  if (tip < 0) { tip = 0; }

  return baseTotal + tip + tax + fee;
}
{% endcodeblock %}


<h2>Question 11: Array Mapping</h2>

<p>For this question, I was looking for code that used a <code>map</code> method to return an array by running a function on each item in the array. Some people used a <code>forEach</code> method to iterate over the array instead, and then push the results to a new array they created. I guess this is OK, but it&rsquo;s not my preference.</p>

{% codeblock lang:javascript %}
// 11: given the following data, write code that returns an array
// containing the name of each item, followed by a comma-separated list of
// the item's extras, if it has any. e.g.
//
//    [ "Salad (Chicken, Steak, Shrimp)", ... ]
//
// (you can assume the library of your choice is available)
var menuItems = [
  {
    id : 1,
    name : 'Salad',
    extras : [
      'Chicken', 'Steak', 'Shrimp'
    ]
  },
  {
    id : 2,
    name : 'Potato',
    extras : [
      'Bacon', 'Sour Cream', 'Shrimp'
    ]
  },
  {
    id : 3,
    name : 'Sandwich',
    extras : [
      'Turkey', 'Bacon'
    ]
  },
  {
    id : 4,
    name : 'Bread'
  }
];
{% endcodeblock %}


<p>Here&rsquo;s an answer:</p>

{% codeblock lang:javascript %}
var newArray = dojo.map(menuItems, function(item) {
  var ret = item.name;
  if (item.extras && item.extras.length) {
    ret += '(' + item.extras.join(', ') + ')';
  }
  return ret;
});
{% endcodeblock %}

<h2>Bonus 1: Functional Programming 101</h2>

<p>This was a late addition, courtesy of <a href="http://andrew.hedges.name/">Andrew Hedges</a>, so not everyone saw it:</p>

{% codeblock lang:javascript %}
// BONUS: write code such that the following alerts "Hello World"
say('Hello')('World');
{% endcodeblock %}

<p>I wanted to see people understand that functions could return other functions, and that the returned function has access to the scope of the wrapper function:</p>

{% codeblock lang:javascript %}
var say = function(first) {
  return function(second) {
    alert(first + ' ' + second);
  }
};
{% endcodeblock %}


<p>Some people got pleasantly carried away with this question; check out this <a href="http://jsfiddle.net/t8cAK/">JSFiddle</a> from Colin Snover.</p>

<h2>Bonus 2: Attention to Detail</h2>

<p>This last bonus was riddled with errors, including some that I made accidentally when I wrote it at 2 a.m.</p>

{% codeblock lang:javascript %}
// BONUS: what is the faulty logic in the following code?
// how would you fix it?
var date = new Date(),
    day = date.getDate(),
    month = date.getMonth(),
    dates = [];

for (var i = 0; i <= 5; i++) {
  dates.push(month + '/' + (day + i));
}

console.log('The next five days are ', dates.join(', '));
{% endcodeblock %}

<p>Here&rsquo;s what you should see:</p>

<ul>
<li>The <code>for</code> loop will return 6 dates, not 5.</li>
<li>The method for calculating the date for each successive date could end up with nonexistent dates (32, 33, etc.), and it doesn&rsquo;t change the month when it should.</li>
<li>The <code>getMonth</code> method on the date object returns a zero-indexed month.</li>
</ul>


<p>Here&rsquo;s a fix:</p>

{% codeblock lang:javascript %}
(function() {
  var date = new Date(),
      otherDate = new Date(),
      day = date.getDate(),
      future = 5,
      dates = [],
      i;

  for (i = 1; i <= future; i++) {
    otherDate.setDate(day + i);
    newMonth = otherDate.getMonth() + 1;
    newDay = otherDate.getDate();
    dates.push(newMonth + '/' + newDay);
  }
})();
{% endcodeblock %}


<h2>Postscript</h2>

<p>I want to be really clear that I&rsquo;m not some super-awesome and infallible JavaScript developer, and more to the point, there was a time in the not-too-distant past where I would have failed my own quiz miserably. While my main goal in putting this together was to find some skilled developers to help me out, I also wanted to provide a tool for exposing up-and-coming developers to some slightly more advanced concepts of JavaScript. I hope that, whatever your skill level, you found it to be at least entertaining, and at best, useful. I also hope you&rsquo;ll forgive any gross errors I&rsquo;ve made in the answers above, though I tried <em>really</em> hard to test them all.</p>

<h2>Post-Postscript</h2>

<ul>
<li>To the person who complained that I included code from a library other than jQuery, and who helpfully illustrated their point with the graph that shows jQuery is obviously better than anything that ever was: I&rsquo;m not sure whether to laugh or cry or just be really snarky. I&rsquo;m looking for JavaScript developers. If you don&rsquo;t see the value in knowing more than just jQuery, and if you can&rsquo;t find your way through relatively trivial non-jQuery code, you need not apply.</li>
<li>If you found the questions vague and requiring a lot of assumptions or guesswork, well, welcome to consulting &mdash; if such things make you uncomfortable, we&rsquo;re not going to be a good fit. I was looking for people to tell me what they know, to impress me, to point out where they saw holes in the questions, to take initiative. This was not a multiple choice test; it was an interview.</li>
</ul>


<h2>License</h2>

<p>If you think the quiz would be useful to you as you&rsquo;re looking for a JavaScript developer to call your own, it&rsquo;s licensed under the <a href="http://sam.zoy.org/wtfpl/">WTFPL</a>, which you should read just because it&rsquo;s funny.</p>
