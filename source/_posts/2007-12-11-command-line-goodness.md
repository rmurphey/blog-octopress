--- 
layout: post
date: "2007-12-11"
title: Command line goodness
comments: true
categories: command line, front-end development
---

Two good ones from today:

<strong>cygwin/perl</strong>: change all references to <code>portfolio.html</code> to <code>portfolio_d.html</code>.

<div class="CodeRay">
  <div class="code"><pre>perl -i -p -e 's/portfolio\.html/portfolio_d.html/g' *.html</pre></div>
</div>


More perl command line goodness <a href="http://articles.techrepublic.com.com/5100-22-1044668.html">here</a>.

<strong>vim</strong>: on every line that has an element with an id attribute, take the value of that attribute and use it as the argument for a php command.

<div class="CodeRay">
  <div class="code"><pre>g/id=&quot;\([^&quot;]\+\)&quot;/ s/id=&quot;\([^&quot;]\+\)&quot;/value=&quot;p('\1'); ?&gt;&quot; id=&quot;\1&quot;/</pre></div>
</div>


More on vim and regular expressions <a href="http://www.geocities.com/volontir/">here</a>. Also: <a href="http://www.linux.com/articles/57727">shell commands from inside vim</a>.

I've been gradually moving completely away from Dreamweaver as my editor because I really prefer the mouse-less navigation of vim, and stuff like this helps me replicate features I miss (like search and replace across multiple files) and take them to a whole new level at the same time.