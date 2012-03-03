--- 
layout: post
date: "2007-12-19"
title: HTML entities from AJAX into input fields using jQuery
comments: true
categories: jquery, troubleshooting
---

I was receiving a JSON string that contained an HTML entity, like this:

<div class="CodeRay">
  <div class="code"><pre>{ myValue: '€30,000' }</pre></div>
</div>


I was supposed to put the value of <code>myValue</code> into a text input. I assigned the value to a variable and tried:

<div class="CodeRay">
  <div class="code"><pre>$('#myInput').val(myValue);</pre></div>
</div>


but that was putting the string, literally, into the input:

<div class="CodeRay">
  <div class="code"><pre>€30,000</pre></div>
</div>


— this in spite of the fact that this works just fine:

<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>


I'm not sure if this is a case of jQuery getting a little overzealous in its helpfulness or what — some Googling didn't turn up much, and I needed to move on. So here's what I did:

<div class="CodeRay">
  <div class="code"><pre>$('body').append('');

// each time an input needs to be updated ...
$('#temp').html(foo);
$('#myInput').val($('#temp').html());</pre></div>
</div>


It's a little hacky, but not so hacky that I feel bad about it, and I guess I'm telling myself that its hacky-ness is not very heavy, and it's outweighed by the time I'd have had to spend to solve the root problem.