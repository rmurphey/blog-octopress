---
layout: post
date: "2007-12-14"
title: jQuery IE7 "Operation Aborted" error
comments: true
categories:
  - front-end development
  - jquery
  - troubleshooting
---

I just got done troubleshooting an issue with a page with some jQuery on it. The jQuery was in a script called from the head of the document, and its job was to rearrange some elements into tabs once the page was loaded.

In unpredictable cases (sequential reloads had different results), loading the page in IE7 would lead to an "Operation aborted" error that provided no additional information. Through some googling, I found out that this was probably related to IE choking when some script was run against the DOM before the DOM was fully loaded.

What puzzled me was that I was using <code>$.ready()</code> to wrap my jQuery, which was supposed to wait for the DOM, but it didn't seem to matter. Then I found <a href="http://channel9.msdn.com/ShowPost.aspx?PostID=215410#215410">this</a> -- it turns out that interacting with the DOM via javascript from within a table can make IE all sorts of angry, while interacting with the DOM via javascript from within a div is just fine.

While my case doesn't directly match the example above, it seems very possible that the table-based template I was writing for was the source of the headaches. Changing the base HTML template for the page I was working on wasn't immediately in the cards, so I split the offending js file into two pieces -- one in the head of the document that contains some basic functions but didn't actually do anything, and another right before the closing body tag that does the actual talking to the DOM -- and that seemed to fix it.
