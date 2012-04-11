---
layout: post
title: "Greenfielding"
date: 2012-04-10 12:19
comments: false
categories:
---

I'm officially one-third of the way through my self-imposed month of unemployment before I join [Bocoup](http://bocoup.com) at the beginning of May, and I've been spending most of what would normally be my working hours on a small demo to support talks at [conferences](http://2012.front-trends.com/) I will be [speaking at](http://backboneconf.com/) this [summer](http://2012.texasjavascript.com/). It's just a [little app](https://github.com/rmurphey/srchr-demo) that searches various services, and displays the results -- so simple that, when I showed it to Melissa, she helpfully asked why I wouldn't just use Google.

It's been about 18 months since I last got to start a project from scratch -- in that case, the codebase that became [Mulberry](http://mulberry.toura.com) -- but even then, I didn't have control over the full stack of technologies, just the JavaScript side of things. Over the course of my time on that project, I came to be extremely familiar with Dojo, fairly competent with Jasmine, decently comfortable with Ruby and its go-to simple server Sinatra, and somewhat conversational in Sass.

I spent most of my time on that project working with technologies with which I was already pretty comfortable. Interactions with new technologies came in dribs and drabs (except for that one time I decided to test my Ruby skills by rewriting our entire build process), and all of my learning was backed up by a whole lot of institutional knowledge.

The consulting world, of course, is a wee bit different: you interact frequently with new technologies, and you never know what a client might ask you to do. Learning comes in bursts, and the ability to quickly come up to speed with a technology is imperative. On a six-week project, you can't spend the first 40 hours getting your bearings.

Even though I spent three years as a consultant, returning to that world of constant learning was feeling a tad intimidating. And so for this project, I decided to make a point of leaving that comfort zone, and intentionally chose technologies -- Node, Bootstrap, Backbone, Mocha, RequireJS -- that I hadn't really had a chance to work with in depth (or at all).

## On Learning

Greenfield projects are few and far between, and it's easy to get in a rut by sticking with the tools you already know. Some of my most exciting times at Toura weren't when I was writing JavaScript, but rather when I was learning how to talk to the computer in a whole new language. Greenfielding a personal project is a special treat -- it never really has to be "finished," and no one's going to be mad at you if it turns out you made a shitty choice, so you're free to try things that are less of a sure thing than they would need to be if you were getting paid.

Speaking personally, it can also be a little intimidating to learn a new thing because learning often involves asking for help, and asking for help requires admitting that I don't already know how to do the thing that people might expect I already know how to do.

Sometimes the thing that gets in the way of starting a new learning project is actually the fear that I will get stuck. What does it mean if the person who talks at conferences about principles code organization can't figure out how best to structure a particular app with Backbone? What does it mean if the person who's been encouraging people to build their JavaScript can't get RequireJS to generate a proper build? What will I say to [Isaac](http://blog.izs.me/), now that he is standing in front of me and introducing himself, when I have not in fact spent any quality time with Node prior to this past weekend?

Lucky for me, it turns out that all of this is mostly in my head. While I often preface my questions with a small dose of humility and embarrassment, it turns out that well articulated questions are usually greeted with thoughtful and helpful answers. If anything, I'm trying to be more communicative about the learning that I do, because I think it's important that people feel comfortable acknowledging that they used to not know a certain thing, and now they do. I also try to gently remind people that just because they have known something for months or years doesn't mean they should look down upon the person enthusiastically blogging about it today.

On that note ... here's what's new to me these past couple of weeks :)

## Twitter Bootstrap

I've written a lot of demo apps, and while my coding style has changed over the years, one thing has remained constant: they all look fairly terrible. In theory, we're all smart enough to know that what a demo looks like doesn't have any bearing on what it explains, but in reality a good-looking demo is simply more compelling, if only because the viewer isn't distracted by the bad design.

With this in mind, I decided to give [Twitter Bootstrap](http://twitter.github.com/bootstrap/) a try. When I first arrived at the site, I started looking for docs about how to set it up, but it turns out that I vastly overestimated Bootstrap's complexity. Drop a style tag into your page (and, optionally, another style tag for the responsive CSS), look at the [examples](http://twitter.github.com/bootstrap/examples.html), and start writing your markup.

What I really loved is that there were patterns for everything I needed, and those patterns were easy to follow and implement. Within an hour or so I had a respectable-looking HTML page with markup that didn't seem to suck -- that is, it looked good *and* it was a decent starting point if I ever wanted to apply a custom design.

## Node

If you've ever talked to me about Node, you know that I have pretty mixed feelings about it -- some days I feel like the people writing JavaScript in the browser really would have benefited if the people who have gravitated to Node had stuck around to invest their collective smarts in the world where 99% of JavaScript is still written. But that doesn't really have anything to do with Node the technology, so much as Node the new shiny thing unburdened by browser differences.

I've actually visited Node a couple of times previously -- if you haven't at least installed it, you might be living under a rock -- and I was flattered that [Garann](http://dev.garann.com) asked me to review her book [Node for Front-End Developers](http://shop.oreilly.com/product/0636920023258.do), but past experiences had left me frustrated.

This time, something was different. I don't rule out that it might be me, or even that learning some of the ins and outs of Ruby might have prepared me to understand Node -- and packages and dependency management and writing for the server instead of the browser -- better this time around. It could also be that the Node ecosystem has reached a point of maturity that it just hadn't reached the last time I poked around.

Regardless, I found that everything made a whole lot more sense this time, and my struggles this time were about forgetting to stringify an object before sending it as a response to a request, not about getting the server to start in the first place. I used the `q` module to give me my beloved promises for managing all the asynchronicity, and generally found it ridiculously pleasant to leave behind all the context switching I'd grown accustomed to while using JavaScript and Ruby side by side. I'll probably still turn to Ruby for automating things on the command line (though I continue to be intrigued by [grunt](https://github.com/cowboy/grunt)), but I'm ready to admit that it's time for me to add Node to my toolbox.

## Mocha

To be honest, I'd just planned on using [Jasmine](https://github.com/pivotal/jasmine) for writing tests for this project, mostly because I'd never set up Jasmine myself, and I was interested in maybe getting it working with grunt for headless testing. I ended up bailing on that plan when, in the course of some Googling for answers about Jasmine, I came across [Mocha](http://visionmedia.github.com/mocha/).

Mocha is a super-flexible testing framework that runs on Node and in the browser. You can choose your assertion library -- that is, you can choose to write your tests like `assert(1, 1).ok()` or `expect(1).to.be(1)` depending on your preference. I decided to use the latter style, with the help of [expect.js](https://github.com/LearnBoost/expect.js). You can also choose your reporting style, including the ability to generate docs from your tests.

I had to do a bit of finagling to get the browser-based tests working with my
RequireJS setup, and ultimately I ended up just using my app's server, running
in dev mode, to serve the tests in the browser. I'm still working out how best
to run just one test at a time in the browser, but all in all, discovering Mocha has probably been the best part of working on this project.

## RequireJS

[RequireJS](http://requirejs.org/) is another tool that I've dabbled with in the past, but for the last 18 months I've been spending most of my time with Dojo's pre-AMD build system, so I had some catching up to do. I don't have a ton to say about RequireJS except:

- It's gotten even easier to use since I last visited it.
- The docs are great and also gorgeous.
- While I haven't had to bother him lately, James Burke, the author and maintainer of RequireJS, is a kind and incredibly helpful soul.
- The [`text!`](http://requirejs.org/docs/api.html#text) plugin makes working with client-side templates incredibly simple, without cluttering up your HTML with templates in script tags or hard-coding your templates into your JavaScript.
- The [`use!`](https://github.com/tbranyen/use.js) plugin makes it painless to treat libraries that don't include AMD support just like libraries that do. I hear it might become an official plugin soon; I hope it will.

## Backbone

This part was a tough choice, and I actually set out to use a different framework but ended up getting cold feet -- even though this was just a personal project, it did need to reach some semblance of done-ness in some reasonable period of time. After a little bit of poking around at other options, I decided that, barring completely copping out and using Dojo, Backbone was going to be the best tool for this particular job on this particular schedule.

I'm pretty torn about this, because I decided to use a framework that I *know* has shortcomings and limited magic, and I *know* that other options would serve me better in the long term. But I also know that the long term doesn't exactly matter for this particular project. The thing that swayed me, really, was that with Backbone, I didn't feel like I needed to grasp a whole slew of concepts before I could write my first line of code.

I looked up plenty of things along the way, and rewrote my fair share of code when I discovered that I'd been Doing It Wrong, but I was able to maintain a constant forward momentum. With the other options I considered, I felt like I was going to have to climb a ladder of unknown height before making any forward progress.

I feel like I made the right choice for this project, but it's a choice I'd spend a lot more time on for a "real" project, and I'd be much more inclined to invest the initial energy in getting up to speed if the payoff was clearer. This, though, is a choice that people seem to be consistently terrible at, and so I feel like I should beat myself up about it just a little. It's all too common to dig a ginormous hole for ourselves by choosing the technology that lets us start writing code the soonest; on the flip side, it's all too common to choose a technology that's complete overkill for the task at hand.

## The End

The master branch of the [repo for the project](https://github.com/rmurphey/srchr-demo) should be mostly stable (if incomplete) if you want to check it out. I'm going to close comments on this post in the hopes that you'll write your own post about what you've been learning instead :)
