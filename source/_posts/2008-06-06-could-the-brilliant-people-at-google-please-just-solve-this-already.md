--- 
layout: post
date: "2008-06-06"
title: Could the brilliant people at Google please just solve this already?
comments: true
categories: front-end development, javascript
---

Ajaxian has the latest article I've come across about <a href="http://ajaxian.com/archives/google-analytics-after-onload-and-documentwrite-for-xhtml">remote Javascript using document.write()</a> (and a bit of a related issue when it comes to Google Analytics). They link to <a href="http://weston.ruter.net/projects/xhtml-document-write/#key-htmlparser-modifications">yet another way to get around it</a> by hijacking the browser's document.write(), just as John Resig <a href="http://blog.rebeccamurphey.com/2008/04/11/remote-javascript-with-documentwrite-is-killing-me/#comment-91">suggested</a> to me the last time I complained about this.

I said it before, I said it in my comment on the Ajaxian post, and I'll say it again: could the good folks at Google, and all the other sites that want us to give them traffic and/or money by placing their script tags all over our sites, maybe possibly spend some time coming up with methods that would:
<ul>
	<li>allow script tags to be placed at the end of the HTML</li>
	<li>allow their widgets, ads, whatever to be placed in the page via a DOM-based method, rather than document.write</li>
</ul>
Thanks. I'll be waiting.