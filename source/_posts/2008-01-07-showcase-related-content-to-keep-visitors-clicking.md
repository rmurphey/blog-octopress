--- 
layout: post
date: "2008-01-07"
title: Showcase related content to keep visitors clicking
comments: true
categories: analytics, blogging, seo
---

I've drummed up a lot of visitors by posting links on dzone and StumbleUpon, but the visitors aren't very sticky -- many will visit the single page I linked to and then move on.

Looking at my site exits in Google Analytics, I noticed that my category pages had much lower exit rates than my single post pages. This wasn't exactly a surprise, if I thought about it: a visitor to a single-post might see my list of recent posts, but there was no guarantee that any of them would be related to what they came to the blog to read about. Visitors to category pages, on the other hand, would see a collection of related content.

I wanted to give visitors an easy way to see other posts that might interest them, so I just installed the <a href="http://www.dagondesign.com/articles/other-posts-from-cat-plugin-for-wordpress/">Other Posts from Category</a> plugin. The plugin adds links to posts that are in the same category (or categories) as the main post, and it's very configurable via the Wordpress admin.

It didn't make sense to me to just have the list at the end of the post -- visitors might never make it there to discover other items that would interest them. A jQuery one-liner copied the list and put it at the top of the post, too:

<div class="CodeRay">
  <div class="code"><pre>$('div.ddop').clone().insertAfter('div.post p.byline');</pre></div>
</div>


I'll be watching my pages per visit and site exit metrics to see whether it makes a difference --