--- 
layout: post
date: "2008-01-02"
title: Fixing width issues with IE6 print stylesheets
comments: true
categories: css, ie6, print stylesheet
---

Note to self: remember to use <code>!important</code> when trying to deal with text running off the page in print versions of pages in IE6.

Basic steps to follow:

<ul>
<li>EITHER have separate print and screen stylesheets ...
<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>

</li>
<li>... OR declare separate screen and print rule sets in a single stylesheet
<div class="CodeRay">
  <div class="code"><pre>@media print {
  #foo {
    display:none;
  }
}

@media screen {
  #foo {
    display:block;
  }
}</pre></div>
</div>

</li>
<li>Put the content that you <em>do</em> want printed in containers that you can easily target with a CSS rule. Then, in the print css, <code>display:none</code> all the siblings of the containers that <em>don't</em> need to be printed. It may take a little trial and error to get the lists of printing and non-printing elements right, but well-designed, semantic HTML can make this task easier. Long story short, if you can <code>display:none</code> a parent element, you don't need to also <code>display:none</code> its children. The flipside of that is that if you do <code>display:none</code> a parent, you can't then <code>display:[anything]</code> children elements to get them to show up.</li>
<li>To deal with IE issues, give a fixed width to the containers you do want printed, and make the width <code>!important</code>:
<div class="CodeRay">
  <div class="code"><pre>#content {
  width:400px !important;
  /* 400px seems to work well
  if you want to span the width
  of a letter-sized page */
}</pre></div>
</div>

</li>
<li>Also to deal with IE issues, you may find it useful to put some <code>padding-right</code> on your <code>p</code> elements.</li>
<li>If you have print rules that are required for IE but break things in other browsers, use a <a href="http://www.quirksmode.org/css/condcom.html">conditional comment</a> to include the rules in a separate stylesheet.</li>
</ul>

Further reading:

<ul>
<li><a href="http://blogs.msdn.com/ie/archive/2005/06/10/428149.aspx">Ease the Pain with IE6 Printing</a></li>
<li><a href="http://www.alistapart.com/stories/goingtoprint/">CSS Design: Going to Print</a></li>
</ul>

These are just some notes; interested in hearing other's tips!