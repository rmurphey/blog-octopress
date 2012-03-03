--- 
layout: post
date: "2010-03-15"
title: "srchr: Crowdsourcing JavaScript wisdom"
comments: true
categories: front-end development, javascript, jquery
---

<strong>UPDATE:</strong> The deadline for completing your submission is April 16, the day before <a href="http://jsconf.us/2010/">JSConf</a>. If you're at the conference, join others in the hacker lounge to see what they did!

I've been working on a blog post about using classes and pub/sub for structuring jQuery applications, and I had in mind a pretty simple demo app that I was going to build. I also wanted to show a version of the app that was built in a more traditional way, and I'd been pondering whether I should write that version myself, or see if I could cajole someone else into doing it. And then, a moment of inspiration: rather than a contrived counter-example, why not get a whole bunch of developers to show how they'd tackle the problem, so we can all gain from the exercise and learn from each other?

I tweeted my idea, and five minutes later I had a dozen volunteers and counting, which is downright awesome and in hindsight shouldn't be surprising. It's so rare that we get to see multiple approaches to a moderately complex problem -- it's much more common to see horrendous code and bitch about it :)

<h2>The project</h2>
I've put together a mock/spec for a small, strictly client-side application that uses <a href="http://developer.yahoo.com/yql/console/">YQL</a> to search for content and then displays it to the user. (Click on the image to see it full-size.)

<div class='p_embed p_image_embed'>
<img alt="Media_httpblogrebecca_cudcn" height="232" src="http://getfile2.posterous.com/getfile/files.posterous.com/import-vejw/urlfeGJCicwcdclDnmvkaGkligwndBxhmgielwBbGsledHIkDIJxxhGzfuEG/media_httpblogrebecca_Cudcn.png.scaled500.png" width="300" />
</div>


Think of this as an exercise in creating a product, not a site that you finish and walk away from -- the goal is to create an extensible, modular application. That said, there are no "right" answers here: the point is for you to demonstrate how you, personally, would approach the problem.

<h2>Presenting your solution</h2>
I've created a <a href="http://github.com/rmurphey/srchr">github repository</a> for the project that contains nothing more than some documentation, the mock/spec, and a few stub files and directories. You should fork this repository to get started. If you create some CSS that you'd like to share, I'd encourage you to send a pull request so I can make it available to everyone; this isn't an CSS exercise, so no one should labor over that part if they don't want to. I may very well write some basic CSS myself in the next couple of days, but it's late :)

Finally: please comment on this post if you have any questions!