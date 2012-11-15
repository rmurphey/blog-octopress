---
layout: post
date: "2007-12-12"
title: "jQuery: Build table from JSON data"
comments: true
categories:
  - javascript
  - jquery
---

Given data like:

{% codeblock lang:javascript %}
var data = {
   "GUEST" : {
      'visits' : 1734070,
      'visits_pct' : 74,
      'users' : 1,
      'net_pv' : 3432781,
      'users_pct' : 0,
      'pv_pct' : 13
   },
   'Logged In' : {
      'visits' : 4240,
      'visits_pct' : 0,
      'users' : 177,
      'net_pv' : 188112,
      'users_pct' : 0,
      'pv_pct' : 0
   }
}
{% endcodeblock %}


... a little jQuery ditty to make a table out of the data:

{% codeblock lang:javascript %}
$.each(data,function(rowLabel,v) {
    if (! header) {
        $table.append('');
        var $thead = $('thead tr',$table);
        $thead.append('');
        $.each(v,function(headerLabel) {
            $thead.append('' + headerLabel + '');
        });
        header = true;
    }
    $table.append('');
    var $tr = $('tr:last',$table);
    $tr.append('' + rowLabel + '');
    $.each(v,function(j,cellData) {
        $tr.append('' + cellData + '');
    })
});
{% endcodeblock %}

I'll come back and do something more interesting with this eventually, just wanted to jot it down for now.
