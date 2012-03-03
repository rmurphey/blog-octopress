--- 
layout: post
date: "2011-04-04"
title: A Dojo Boilerplate
comments: true
categories: ""
---

<p>When I first started playing with the <a href="http://dojotoolkit.org">Dojo Toolkit</a>, it was easy enough to <a href="http://dojotoolkit.org/documentation/tutorials/1.6/hello_dojo/">use the CDN-hosted <code>dojo.js</code> and get started</a>, but before long I wanted to make use of one of the features that drew me to Dojo in the first place: the build system that parses your code&rsquo;s dependencies as expressed by <code>dojo.require()</code> statements and creates production-ready files.</p>
<p>Coming from a world where this was entirely a DIY affair, the patterns I should follow for taking advantage of Dojo&rsquo;s system were, shall we say, less than clear. There was a lot of frustration, a lot of swearing, and a lot of pleas for help in #dojo on Freenode.</p>
<p>These days, I&rsquo;m talking about Dojo a lot, and I&rsquo;ve gotten pretty comfortable with how to set up a project &mdash; I even wrote a post about <a href="http://blog.rebeccamurphey.com/scaffolding-a-buildable-dojo-application">scaffolding a Dojo app</a> once I felt like I had the basics down &mdash; but for a long time I&rsquo;ve wanted to release a ready-made starter project, rather than making people follow seven lengthy steps.</p>
<p>With the help of <a href="http://zetafleet.com">Colin Snover</a>, I&rsquo;m pleased to release the <a href="https://github.com/csnover/dojo-boilerplate">Dojo Boilerplate</a>, a simple starter project if you&rsquo;d like to get your feet wet with Dojo and the power of its dependency management and build system. It comes with a bare-bones do-nothing app, a shell script for downloading the Dojo SDK and getting it in the right place, and a shell script and profile file for actually creating a built version. For the brave, it also includes a work-in-progress router for single-page apps &mdash; one of the few features that I feel Dojo itself is missing. Everything you should need to know is documented in the README.</p>
<p>I&rsquo;ve also created a <a href="https://github.com/rmurphey/dojo-demo">small demo app</a> that uses the boilerplate and shows some of the basic concepts of MVC development using Dojo, including separating your code into models, views, controllers, and third-party services. It includes an example of templated widgets, which are one of the biggest selling points of Dojo for me, as well as an uber-basic example of object stores, new in Dojo 1.6.</p>
<p>The goal of the boilerplate and the demo app is to eliminate some of that pain and WTF that I went through &mdash; while Dojo is ridiculously powerful, the barrier to entry can seem daunting. Over and over again, though, I am grateful that I took the time to overcome it.</p>
<p>Finally: as always, pull requests and issues are welcome. Enjoy.</p>
<p>Update: Colin is now the maintainer of the boilerplate; I've updated the links above accordingly.</p>