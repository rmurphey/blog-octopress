---
layout: post
title: "Getting More Out of Deferreds"
date: 2012-04-01 06:29
comments: true
categories: 
---

# Getting More Out of Deferreds

When jQuery [introduced deferreds and promises in the 1.5 release](http://rmurphey.com/blog/2010/12/25/deferreds-coming-to-jquery/), it gave jQuery users a new and much-improved way of dealing with the asynchronicity of JavaScript; with deferreds, it's possible to attach callbacks to asynchronous operations like XHRs after the operation has already begun:

{% codeblock lang:javascript %}
	function makeRequest(url) {
		return $.ajax({url : url }); 
	}

	var req = makeRequest('my-content.html');
	
	req.then(successCallbackFn, errorCallbackFn);
	req.then(anotherSuccessCallbackFn);
{% endcodeblock %}

Both promises and deferreds give you the ability to react to the outcome of an operation, either now if the operation has already "resolved"; or later, when the operation is finally resolved. Since their arrival in jQuery and the arrival of a handy [Underscore implementation](https://github.com/wookiehangover/underscore.Deferred), I've seen lots of code incorporating them. 

## Creating your own deferreds

## Working with maybe-asynchronous operations

## Pre-processing the result of an async operation