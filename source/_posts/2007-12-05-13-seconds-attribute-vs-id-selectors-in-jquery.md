---
layout: post
date: "2007-12-05"
title: "13 seconds: Attribute vs. ID selectors in jQuery"
comments: true
categories:
  - javascript
  - jquery
  - regexp
  - selectors
  - vim
---

Before:

{% codeblock lang:javascript %}
// select all elements with an id or name attribute of fieldName;
// some are inputs (name attribute), some are td's (id attribute)
var $field = $('#' + fieldName + ', [name=' + fieldName + ']');
{% endcodeblock %}

After:

{% codeblock lang:javascript %}
// give inputs both a name and an id attribute,
// and then just select fields and td's via id
var $field = $('#' + fieldName);
{% endcodeblock %}


Savings: 13 seconds over the course of selecting &gt; 100 elements. Thank god for Firebug's profiler, which pointed me to the problem in a few seconds. Attribute selectors in jQuery are handy, OK, but they don't seem to work for large-scale selections.

A side note: vim made the HTML change easy, once I worked through its quirky regexp syntax which requires you to escape the +:

{% codeblock %}
s/name="\([^"]\+\)" /name="\1" id="\1" /g
{% endcodeblock %}
