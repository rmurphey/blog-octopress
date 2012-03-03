---
layout: post
date: "2008-01-10"
title: "Solved: AJAX returns bad results in Internet Explorer"
comments: true
categories: ajax, ie6, internet explorer, javascript
---

Note to self: IE6 likes to cache AJAX requests, and this can be a bad thing if other data on the page that will affect the result of the request has changed.

You'll see this if you have two fields on a page that both contribute to a result, but only send them to the server when one of them has changed.

Let's say field1 and field2 get multiplied on the server side, and both start out with values of 1. So, in IE6:

<ul>
<li>Set field1 to 1000: Response is 1000.</li>
<li>Set field1 to 100: Response is 100.</li>
<li>Set field2 to 123: Response is 12300</li>
<li>Set field 1 back to 1000: <strong>Uh oh! Response is 1000 again!</strong>
</li>
</ul>

IE6 has decided that the result from the first time you set field1 to 1000 was good enough, and it doesn't look to see that the new result is different.

The solution? Add a timestamp to your requests, so each one will look different to IE even if the real data is the same:

{% codeblock lang:javascript %}
// just an example; you'd
// obviously build this from
// the field itself
var data = 'field1=1000';

// build the timestamp
var timestamp = new Date().getTime();
data = data + '&amp;' + timestamp;

// now you can send your data
// using ajax
{% endcodeblock %}

Thankfully, when this happened to me yesterday, I had a coworker who only needed a couple of minutes with the server logs to figure out what was happening. After he figured it out here, I found <a href="http://groups.google.com/group/jquery-en/browse_thread/thread/a40b6fb572232e3b">this thread</a>, which verified the cause, and I was able to solve it in a few minutes.
