--- 
layout: post
date: "2007-12-05"
title: Anchor-based URL navigation with jQuery
comments: true
categories: front-end development, javascript, jquery, progressive enhancement
---

<p><em>This post contains extremely outdated material; for all of your anchor-based jQuery navigation needs, you&rsquo;d do well to check out <a href="http://benalman.com/projects/jquery-bbq-plugin/">Ben Alman&rsquo;s BBQ Plugin</a>.</em></p>
<p>&mdash;</p>
<p>I&rsquo;m working on an application that has several panels of content, each accessed by clicking on a link in the page&rsquo;s main navigation. I wanted to give users the ability to get back to a panel after they&rsquo;d left the application, without having to click on the panel in the navigation again, so I set it up so the URL would include an anchor link indicating the current panel (e.g. <a href="http://www.mysite.com/index.html#panel1">http://www.mysite.com/index.html#panel1</a>). This had the added benefit of encouraging me to be a good front-end developer by following the principles of <a href="http://en.wikipedia.org/wiki/Progressive_enhancement">progressive enhancement</a> &mdash; before I set this up, a user without JavaScript wouldn&rsquo;t get anywhere when they clicked on a link in the page&rsquo;s main navigation, and that&rsquo;s no good. Now, users without Javascript will be taken down the page to the panel they want, and users with Javascript will get the fancy Web 2.0 panels that show and hide and fade and stuff. Here&rsquo;s what I did:</p>
<p>Set up anchor tags at the top of each &ldquo;panel&rdquo; div, and give the anchor tags a class of anchor:</p>
<div class="CodeRay">
  <div class="code"><pre>&lt;div id=&quot;panel1&quot; class=&quot;step&quot;&gt;
  &lt;a class=&quot;anchor&quot; name=&quot;panel1&quot;/&gt;
  ...
&lt;/div&gt;</pre></div>
</div>

<p>Put links to the anchors in the main navigation:</p>
<div class="CodeRay">
  <div class="code"><pre>&lt;ol id=&quot;nav&quot;&gt;
  &lt;li&gt;<a href="#panel1">Panel 1</a>&lt;/li&gt;
  ...
&lt;/ol&gt;</pre></div>
</div>

<p>Set up the onclick behavior for the main navigation items:</p>
<div class="CodeRay">
  <div class="code"><pre>var j = 1;

$('ol#nav li').each(function() {
  $(this).click(function() {
    $('ol#nav li').removeClass('current');
    $(this).addClass('current');
    $('div.panel:visible').hide();
    $('#panel'+j).fadeIn(500);
  });
  j++;
});

// don't include return false! you want the URL
// to change to reflect which panel is showing --
// the next step will make it so that all of the anchors
// will be at the top of the page, so the fact that this
// returns true won't cause the page to scroll to the anchor.</pre></div>
</div>

<p>Move all the anchor tags to the top of the page so users with Javascript won&rsquo;t get bumped down the page when they click on a link:</p>
<div class="CodeRay">
  <div class="code"><pre>$('a.anchor').remove().prependTo('body');</pre></div>
</div>

<p>You might not want to include this step, or you might want to move the anchor tags elsewhere. This worked for me in this case, though.</p>
<p>When a user arrives on the page, look to see if they&rsquo;ve requested a specific panel:</p>
<div class="CodeRay">
  <div class="code"><pre>var myFile = document.location.toString();
if (myFile.match('#')) { // the URL contains an anchor
  // click the navigation item corresponding to the anchor
  var myAnchor = '#' + myFile.split('#')[1];
  $('ol#nav li a[href=&quot;' + myAnchor + '&quot;]').parent().click();
} else {
  // click the first navigation item
  $('ol#nav li:first').click();
}</pre></div>
</div>

<p>The end. Now users who arrive via a URL that includes an anchor tag will go to the proper panel, and everyone else will get the default behavior of going to the first panel.</p>