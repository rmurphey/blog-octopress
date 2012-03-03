--- 
layout: post
date: "2008-01-04"
title: Salivating over server-side Javascript
comments: true
categories: front-end development, javascript, thoughts
---

I came across <a href="http://unclescript.blogspot.com/2008/01/end-of-web-frameworks.html">The End of Web Frameworks</a> in my dzone RSS feed this morning, and it echoes a thought that runs through my brain as I get more and more comfortable with jQuery: who needs the server when you've got Javascript? Wouldn't it be great if the server just handed out data, and the client figured out what to do with it? After talking directly to the DOM for the last several months, a trip back to PHP left me utterly disoriented for a few minutes -- why, exactly, couldn't I take the anchors from that unordered list I'd just built 50 lines ago and reuse their hrefs for something else?

As a <a href="http://unclescript.blogspot.com/2008/01/end-of-web-frameworks.html#c8132224153158413680">couple</a> of <a href="http://unclescript.blogspot.com/2008/01/end-of-web-frameworks.html#c5149909265291802708">commenters</a> pointed out, it's not that easy -- there's still graceful degradation (the yin to progressive enhancement's yang?) to worry about. It's easy to say "oh, I don't have to worry about that," but until the day that's actually <em>true</em>, Javascript on the client side simply can only supplement good, solid, server-generated markup.

That's why stuff like <a href="http://groups.google.com/group/jquery-en/browse_thread/thread/38d8d0c295b2919b">this discussion about server-side jQuery</a> has me nearly salivating. I'm imagining getting JSON data from a simple bit of server-side code that's not encumbered by messy markup and then writing some Javascript to build the DOM of my dreams. The added bonus would be server-side code that could serve that clean, pretty JSON data up to anyone I wanted to have it.

Of course, that DOM would still need to use semantic HTML and not rely on client-side Javascript for anything -- and not just because a visually or otherwise impaired person might drop by someday and want to use my site. <a href="http://blogs.cnet.com/8301-13530_1-9782891-28.html">Progressive enhancement is good for SEO</a>, too, (and so are web standards in general). One only wishes that would lead SEO-focused companies to fall all over themselves in pursuit of it :)