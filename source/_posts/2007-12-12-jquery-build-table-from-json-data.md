--- 
layout: post
date: "2007-12-12"
title: "jQuery: Build table from JSON data"
comments: true
categories: javascript, jquery
---

Given data like:

<div class="CodeRay">
  <div class="code"><pre>var data = {
   &quot;GUEST&quot; : {
      &quot;visits&quot; : 1734070,
      &quot;visits_pct&quot; : 74,
      &quot;users&quot; : 1,
      &quot;net_pv&quot; : 3432781,
      &quot;users_pct&quot; : 0,
      &quot;pv_pct&quot; : 13
   },
   &quot;Logged In&quot; : {
      &quot;visits&quot; : 4240,
      &quot;visits_pct&quot; : 0,
      &quot;users&quot; : 177,
      &quot;net_pv&quot; : 188112,
      &quot;users_pct&quot; : 0,
      &quot;pv_pct&quot; : 0
   }
}</pre></div>
</div>


... a little jQuery ditty to make a table out of the data:

<div class="CodeRay">
  <div class="code"><pre>$.each(data,function(rowLabel,v) {
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
});</pre></div>
</div>


I'll come back and do something more interesting with this eventually, just wanted to jot it down for now.