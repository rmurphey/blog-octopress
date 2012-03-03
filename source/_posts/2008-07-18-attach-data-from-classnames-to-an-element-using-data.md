--- 
layout: post
date: "2008-07-18"
title: Attach data from classnames to an element using $().data()
comments: true
categories: front-end development, howto, jquery
---

At <a href="http://dailystrength.org">DailyStrength</a>, we're working on an abstract way to attach a Javascript behavior (popping a modal, sliding in a form) to objects -- journals, comments, etc. -- being displayed on the site. We wanted to attach some information to the objects by putting their type and ID in classnames. Then it would be up to Javascript to extract that data if it needed it. Previously, I'd done this on an as-needed basis with a function custom to the object and behavior, but I wanted a way to do it going forward without thinking about it.

I'd read about the <a href="http://docs.jquery.com/Core/data">$().data()</a> function that was made available in jQuery 1.2.3, and figured I could leverage it for the purpose. Now, given an element like
<div class="CodeRay">
  <div class="code"><pre>an element</pre></div>
</div>


I can use a simple plugin to assign data to an element:
<div class="CodeRay">
  <div class="code"><pre>{
  key1 : [ value1, value2, value3],
  key2 : value4
}</pre></div>
</div>

This means that I can then do <code>$(element).data('key1')</code> and get back the array. I also added a little helper to get back all of the keys for an element, so I can do:
<div class="CodeRay">
  <div class="code"><pre>$(element).getDataFromClassname();
var element_keys = $(element).data('keys');
var element_data = [ ];
$.each(element_keys, function(key,value) {
  element_data[key] = value;
});</pre></div>
</div>

Here's the simple plugin:
<div class="CodeRay">
  <div class="code"><pre>$.fn.getDataFromClass = function() {
        return $(this).each(function() {
                var $this = $(this);
                if ($this.attr('class').match('_')) {
                        var classes = $this.attr('class').split(' ');
                        var keys = [ 'keys' ];
                        $.each(classes, function(i,c) {
                                if (c.match('_')) {
                                        var parts = c.split('_');
                                        var prefix = parts[0];
                                        keys.push(prefix);
                                        var data;
                                        if (parts[1].match('-')) {
                                                data = parts[1].split('-');
                                        } else {
                                                data = parts[1];
                                        }
                                        $this.data(prefix,data);
                                }
                        });
                        $this.data('keys',keys);
                }
        });
};</pre></div>
</div>