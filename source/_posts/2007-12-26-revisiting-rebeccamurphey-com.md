--- 
layout: post
date: "2007-12-26"
title: Revisiting rebeccamurphey.com
comments: true
categories: front-end development, javascript
---

In the summer of 2006, I decided to put together a small web site to show the work I'd been doing, in hopes of landing a full-fledged web job. It didn't take long before I had an offer from <a href="http://www.webslingerz.com">webslingerz</a>, where I've been for the last year and a half.

I've updated my resume a few times since then, and added a couple of items to my portfolio, but the more I learned about HTML, CSS and Javascript, the less stomach I had for going back to see whether what I'd done in the code still made sense. I knew that I'd leaned heavily on text stored in Javascript arrays, out of sight of search engines; I also knew that, in hindsight, my level of Javascript proficiency at the time was probably laughable, and there would be lots to rethink.

Well. Today I took a deep breath and went back to my portfolio site to see the damage my 2006 self had done. And oh what a mess. My HTML was riddled with excessive classes and divs and less-than-semantic markup. I, too, had used gems like <div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>
 and the hilarious <div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>
 which would only be funnier if it were <div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>
<li class="item">
Javascript arrays as content management system had proven over time to be, um, difficult to maintain, not to mention useless for SEO.

Before anything else, I got the site moved over to www.rebeccamurphey.com to line it up with the blog's URL, and added an .htaccess file to root of the old domain to tell it to send any requests to the new domain.

After that, my first step was to clean up the markup and get all of the content out of Javascript and into HTML -- the old site just had empty content divs that were populated from Javascript, and that made me want to cry. The old site also had multiple files for the portfolio section; I combined their content into one file, with each section set aside in a div. Once that was done, I opened the pages in Firefox, stripped out the old CSS, and pasted in this blog's CSS as a starting point. It was actually pretty remarkable how far <a href="http://www.456bereastreet.com/archive/200711/posh_plain_old_semantic_html/">POSH</a> got me -- most of my CSS edits were to remove unnecessary rules.

With the CSS and HTML in good shape, it was time to tackle the Javascript. To start with (no surprise here) I ditched Prototype and Scriptaculous in favor of jQuery. Looking back, the old incarnation had done everything backwards. The old HTML included a menu but no content; clicking on a menu item called a function which grabbed the appropriate content and put it into the appropriate div. In the new version, on both the Work and Resume pages, it's the menu that's built from the content, using my <a href="http://blog.rebeccamurphey.com/2007/12/24/jquery-table-of-contents-plugin-nested/">table of contents plugin</a>. A little more Javascript added <a href="http://blog.rebeccamurphey.com/2007/12/24/anchor-based-url-navigation-jquery-plugin/">anchor-based navigation</a>.

<div class="CodeRay">
  <div class="code"><pre>$('document').ready(function() {
  $.toc('#panels h2,#panels h3').
    attr('id','toc').
    insertAfter('#header');

  $('#toc li a').each(function() {
    var $a = $(this);
    var href = $a.attr('href');
    href = href.replace('#','');
    var $anchor = $('#panels a.anchor[name=&quot;' + href + '&quot;]');

    if ($anchor.parent().is('div.panel')) {
      var subpanel = false;
      var $panel = $anchor.parents('div.panel');
    } else {
      var subpanel = true;
      var $panel = $anchor.parent();
    }

    $a.click(function() {
      if (subpanel) {
        $panel.siblings().hide();
        $panel.fadeIn();
        var $panelParent = $panel.parents('div.panel');
        $panelParent.siblings().hide();
        $panelParent.show();
      } else {
        $panel.siblings().hide();
        $panel.find('li').show();
        $panel.fadeIn();
      }
    });

  });

  $('#panels a.anchor').remove();

  var url = document.location.toString();

  if (url.match('#')) {
    var urlAnchor = url.split('#')[1];
    var $a = $('#toc a[href=&quot;#' + urlAnchor + '&quot;]');
    $a.click();
  }
});</pre></div>
</div>


In the end: <a href="http://www.rebeccamurphey.com">the new and improved <a href="http://www.rebeccamurphey.com">www.rebeccamurphey.com</a></a>.

<a href="http://blog.rebeccamurphey.com/2007/12/24/learning-css-web-design/">As I said the other day</a>, I'm constantly being reminded of what I didn't know at some point in the past when I thought I knew a whole lot more, and though I dreaded digging in to this, it was actually kind of fun. I'm hoping I might make a holiday tradition out of revisiting this site and being reminded of how much more I've learned in the meantime.</li>