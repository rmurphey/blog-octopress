---
layout: post
date: "2008-12-07"
title: jQuery Style Guide from Benjamin Sterling
comments: true
categories: jquery
---

Just wanted to call attention to two excellent posts by Benjamin Sterling: <a href="http://benjaminsterling.com/better-jquery-code-1/">Better jQuery Code 1</a> and <a href="http://benjaminsterling.com/better-jquery-code-2/">Better jQuery Code 2</a>. He offers some must-have tips on style, form and best practices. Thanks to <a href="http://twitter.com/foobar2k">@foobar2k</a> for the link.

One addition to his notes on caching selections by setting up references: I'm partial to giving references a variable name that starts with a dollar sign, like $links. For me, it serves as a good reminder that the variable is a reference to a jQuery object.
