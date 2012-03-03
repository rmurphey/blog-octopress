---
layout: post
title: "Switching to Octopress"
date: 2011-07-25 08:14
comments: true
categories:
---

I'm taking a stab at starting a new blog at
[rmurphey.com](http://rmurphey.com), powered by
[Octopress](http://octopress.org/), which is a set of tools, themes, and other
goodness around a static site generator (SSG) called
[jekyll](https://github.com/mojombo/jekyll). A couple of people have noticed
the new site and wondered what I'm doing, so I thought I'd take a couple of
minutes to explain.

My old blog at [blog.rebeccamurphey.com](http://blog.rebeccamurphey.com) is
managed using [Posterous](http://posterous.com). It used to be a self-hosted
WordPress site, but self-hosted WordPress sites are so 2009. One too many
attacks by hackers made it way more trouble than it seemed to be worth.
Posterous made switching from a WordPress install pretty easy, so, I did that.
All told, it took a few hours, and I was pretty happy.

For a few reasons, the old blog isn't going anywhere:

- I ran into some trouble importing the old content into jekyll. I was tired
  and I didn't investigate the issues too much, so they're probably solveable,
  but ...
- Some of the old content just isn't that good, and since time is a finite
  resource, I don't want to get too wrapped up in moving it over. Plus ...
- Frighteningly or otherwise, some of my posts have become reference material
  on the internet. If I move them, I've got to deal with redirections, and I
  have a feeling that's not going to be an easy task with Posterous.

In hindsight, I should have switched directly from WordPress to an SSG. Despite
my many complaints about Posterous -- misformatted posts, lack of comment
hyperlinks, a sign-in requirement for commenting, and lots more -- in the end
my decision to switch to a static site generator instead was more about having
easy control over my content _on my filesystem_.

[This article](http://blog.guestlistapp.com/post/2304152860/five-reasons-to-use-a-static-site-generator-instead-of)
explains it well, but the bottom line, I think, is that static site generators
are blogging tools for people who don't need all the bullshit that's been added
to online tools in the interest of making them usable by people who don't know
wtf they're doing. So, yes, to use an SSG, you have to know wtf you're doing,
and for me that's a good thing: the tool gets out of my way and lets me focus
on the writing.

As for Octopress, it seems pretty damn nifty -- the default theme looks gorgeous on my
desktop and on my phone, and it seems they've taken care to put common
customization points in a single sass file. All that aside, though, one of my favorite parts
about it is that my content is truly my content. If Octopress pisses me off --
though I hope it won't! -- then I can simply take my markdown files and put
them in some other SSG, upload the whole thing to my GitHub pages, and be done
with it. Win all around.
