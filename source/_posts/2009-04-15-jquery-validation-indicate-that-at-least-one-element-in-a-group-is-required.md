---
layout: post
date: "2009-04-15"
title: "jQuery validation: Indicate that at least one element in a group is required"
comments: true
categories: form validation, jquery, plugins
---

I had a need today to indicate that at least one of a set of input fields was required. I was hoping there was a direct way to do this in the <a href="http://bassistance.de/jquery-plugins/jquery-plugin-validation/">jQuery validation plugin</a>; while the method isn't quite as straightforward as I was wishing for, it's still fairly simple.

To start with, I put <code>class="required_group"</code> on each of the elements in the group. Then, I added a custom validation method:

{% codeblock lang:javascript %}
jQuery.validator.addMethod('required_group', function(val, el) {
    var $module = $(el).parents('div.panel');
    return $module.find('.required_group:filled').length;
});
{% endcodeblock %}


... a custom class rule to take advantage of the new method:

{% codeblock lang:javascript %}
jQuery.validator.addClassRules('required_group', {
    'required_group' : true
});
{% endcodeblock %}

... and finally a custom message for the new method:

{% codeblock lang:javascript %}
jQuery.validator.messages.required_group = 'Please fill out at least one of these fields.';
{% endcodeblock %}

What I'd love to see is a way to specify a dependent group without using a custom class rule, but I'm not sure what this would look like, as all validation rules are either keyed off an element's class or the presence of the element's name in the rules object. Thoughts? I'm open to the possibility that there's a far better way to solve this --
