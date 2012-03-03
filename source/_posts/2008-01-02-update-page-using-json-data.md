---
layout: post
date: "2008-01-02"
title: Update page using JSON data
comments: true
categories: howto, jquery, plugins
---

<a href="http://www.rebeccamurphey.com/jquery/updateWithJSON/jquery.updateWithJSON.js">updateWithJSON</a> is a jQuery plugin that updates elements on your page based on key/value pairs in a JSON object. Usage:

{% codeblock lang:javascript %}
$.updateWithJSON(jsonData)
{% endcodeblock %}

<a href="http://www.rebeccamurphey.com/jquery/updateWithJSON/">Here's a demo</a>, and here's how it works:

<ul>
<li>iterate over each property/value combination in the JSON object</li>
<li>look for an element in the DOM that matches the property name
<ul>
<li>first, look for an element with a matching <code>id</code> attribute</li>
<li>if no element with a matching ID is found, look for input, select or textarea elements with a matching <code>name</code> attribute</li>
</ul>
</li>
<li>update the value, contents or selection of the matched element(s) based on the value in the JSON object</li>
</ul>

NOTE: If you have multiple checkboxes with the same <code>name</code> attribute, or a select that allows multiple values, you'll need to pass the values of the selected items in an array:

{% codeblock lang:javascript %}
{
  text_input: 'value1',
  checkbox_group: ['value1','value2','value3'],
  multi_select: ['value4','value5','value6']
}
{% endcodeblock %}


If you use a <code>name</code> attribute like "input[]" on a set of related checkboxes, remember that you'll need to quote it in the JSON object:

{% codeblock lang:javascript %}
{
  'input1[]': ['value1','value2','value3']
}
{% endcodeblock %}


<h3>The back story</h3>
For a recent project, I needed to update elements on a page based on some server-side calculations. JSON is a handy way to transport data from the server back to the browser, and jQuery's <code>getJSON()</code> method makes it painless. So, for example, I was getting back

{% codeblock lang:javascript %}
{ foo: 'bar' }
{% endcodeblock %}

with which I needed to update an element corresponding to "foo" and give it the value "bar". Simple enough; on the page I was dealing with, it was easy to put a unique ID on every element that would need to be updated, and I matched that ID with the property name in the JSON object. It was (almost) as simple as this:

{% codeblock lang:javascript %}
$.each(data,function(name,value) {
  $('#'+name).val(value);
}
{% endcodeblock %}

It's not always the case, though, that you can easily put a unique ID on each element that will need updating; for example, <a href="http://us.php.net/manual/en/language.variables.external.php#77344">PHP rewards you</a> if you put the same <code>name</code> attribute on related checkboxes. Having checkboxes with the same name but different IDs can quickly get confusing when you're dealing with data on the client side and on the server side. The solution above worked in my particular case, but I wanted something that would work more broadly -- say, on pages that had related checkboxes -- and that's how I came up with the plugin.
