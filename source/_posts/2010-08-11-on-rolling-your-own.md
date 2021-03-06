--- 
layout: post
date: "2010-08-11"
title: On Rolling Your Own
comments: true
categories: dojo, jquery, large-applications
---

<p>There&rsquo;s been a lot of activity around my last post, <a href="http://blog.rebeccamurphey.com/on-jquery-large-applications">On jQuery &amp; Large Applications</a>. A number of people have asked me why, exactly, I&rsquo;m so opposed to using jQuery as part of a roll-your-own solution.</p>

<p>To answer that, let&rsquo;s start by looking at (some of) the pieces my ideal large application solution might include:</p>

<ul>
<li>DOM manipulation tools</li>
<li>Ajax tools</li>
<li>A dependency management and build system</li>
<li>Clear patterns for code organization, such as namespaced modules</li>
<li>An inheritance system, preferably one that offers multiple inheritance, for sharing code across modules and staying DRY</li>
<li>A non-DOM-centric, loosely coupled API for communication between modules</li>
<li>A widget system that makes use of the inheritance system, with lifecycle management (setup/teardown) and templating</li>
<li>A system for maintaining templates separate from JavaScript while interning them into the build to eliminate HTTP requests</li>
<li>A system for abstracting RESTful server communication</li>
<li>For a UI-intensive project, a rich widget system pluggable to arbitrary data sources and designed with an easily-extended API</li>
<li>For an enterprise project, a11y and i18n provisions, as well as clear patterns for file organization</li>
</ul>


<p>To all of you who have said you can do this yourself, you win. I can&rsquo;t argue with you without sounding like I&rsquo;m saying you&rsquo;re too dumb, and you&rsquo;re probably not.</p>

<p>But here&rsquo;s my question: why? What, exactly &mdash; <strong>exactly</strong> &mdash; do you gain by putting all of this together for yourself, rather than using the pieces you need of a toolkit that provides all of this out of the box? Because here are a few things I think you lose:</p>

<ul>
<li>Integration. Is your abstracted RESTful data API designed to talk to your widget system that&rsquo;s designed to talk to your template system? That&rsquo;s hott. Now what about when a new version of one of those components comes out that violates an assumption you made?</li>
<li>Maintenance. I heart the good folks who have put together individual answers to these individual questions, but they have no obligation to continue being the good folks they are, and they certainly have no obligation to do it on any sort of schedule. Remember all those plugins that broke with jQuery 1.4? That sure was fun.</li>
<li>Documentation. I&rsquo;m going to grant you, right now, that jQuery is one of the best-documented JavaScript libraries out there, hands down. But what about all these other pieces you&rsquo;re putting together? Especially the ones you really are rolling on your own, like that templated widget thing that communicates so nicely with your abstract data API. There are a wealth of resources for understanding, troubleshooting, and using these pieces in established toolkits. Where will the next developer turn when they have questions about yours?</li>
<li>Experience. Like I said, I get that you&rsquo;re smart. Possibly smarter than me. That&rsquo;s cool. But are you smarter than the combined wisdom of a team of people that has been thinking about these questions for years? Are you sure your solution has thought through all the questions they have?</li>
</ul>


<p>I&rsquo;ve noticed that, in the conversations I&rsquo;ve had the last few days, it seems to fall to me to &ldquo;prove&rdquo; that a roll-your-own solution that includes jQuery isn&rsquo;t advisable. Perhaps that&rsquo;s fair &mdash; &ldquo;you started it!&rdquo;, you might say, and that I did. But simultaneously, others argue that jQuery never set out to answer these questions, and so it&rsquo;s not jQuery&rsquo;s fault that people are trying to use it in ways it wasn&rsquo;t intended to be used. I have waited in vain to hear a compelling reason why jQuery <em>should</em> be part of a large application solution, to hear why I should recommend a roll-your-own solution that includes jQuery to my clients. The extent of the argument seems to be &ldquo;because I like it, and it doesn&rsquo;t force me to think a certain way.&rdquo;</p>

<p><a href="http://www.youtube.com/watch?v=vciEDI3dD8I">No one puts baby in a corner</a>. Got it. But the straw man-ness of this argument has me, literally, chuckling right now. Let&rsquo;s not confuse a mythical one-size-fits-all solution with a toolkit that provides, well, <em>tools</em>. Tools to do all sorts of things, tools meant to work together, tools developed and tested and maintained by a whole big team of smart people, tools that are, actively, being used in really frigging big, really frigging enterprisey applications.</p>

<p>I very purposefully didn&rsquo;t propose a particular alternate solution in the original post, but it&rsquo;s hardly a secret that my personal favorite, of late, has been Dojo. Not because it purports to solve every problem or prescribes how to solve them, but because it gives me so many tools to use to solve a given problem. Time and again I find that &ldquo;Dojo already did that&rdquo; &mdash; they already wrote the tool I’m wishing I had. Now I don&rsquo;t have to write it, and, perhaps more importantly, I know it was written to work with all of the pieces I&rsquo;m already using, and when I use it I’m not risking duplication of code or a lack of testing, maintenance or support. Win.</p>

<p>But let&rsquo;s be very clear: no one&rsquo;s forcing me to use that component! No one is forcing me to do things a certain way, any more than jQuery is &ldquo;forcing&rdquo; me to think of my application entirely in terms of the DOM. I can write my own component if I want, or use someone else&rsquo;s if I want, or change it a bit if I want! For example, on a current project I pulled in mustache.js because the project had a lot of templates that had already been written to use it. The brilliant thing, though, was that integrating mustache.js into dijit._Templated instead of the standard templating system was trivial. That component, and all the others in Dojo and Dijit, are architected explicitly <em>not</em> to be one-size-fits-all. They provide a rock-solid base for large application development, for getting up and running quickly using a bevy of ready-made solutions, but also provide so many extension points that you can turn those solutions on their head if you want or need.</p>

<p>Garann Means, whose blog you should be reading, took a bit of issue with my original post in <a href="http://www.garann.com/dev/?p=186">model-view-controller and comfy clothes</a>.</p>

<blockquote class="posterous_medium_quote"><p>I do agree that it benefits everyone to be working in the same setup and making use of tools that have been vetted by geniuses whose entire job is to create such things. But I&rsquo;m dubious about any approach which comes too close to promising one size fits all. If you&rsquo;ve ever sewn a dress, you understand that one size fits all is technically possible, but some people are going be left with a lot of excess while others will scarcely be able to breathe.</p></blockquote>

<p>Carrying on that metaphor, these pieces provided by Dojo &mdash; or any other comprehensive toolkit, for that matter &mdash; are but starter patterns, and thread, and scissors, and pins, and a sewing machine, and OK I&rsquo;m stretching the metaphor, now, but my point is they&rsquo;re definitely not finished one-size-fits-nobody garments. On the other hand, if I decide to use jQuery in a large application, it can feel like I&rsquo;ve been given a black marker and some of that crinkly brown paper, and now it’s up to me to draw a pattern and then come up with all those other pieces, too. Intellectually interesting and pleasingly crafty, perhaps, but not particularly efficient, sustainable, repeatable, or maintainable.</p>

<p>So again I ask, in all seriousness and in hopes of fostering a good discussion: Why? jQuery provides you with DOM, Ajax, and event management tools, but little else. There are tools <em>designed</em> for building large applications, designed to provide all of the pieces I want and so many more it&rsquo;s not even funny, and they provide you with DOM, Ajax, and event management tools, too. What&rsquo;s the compelling case for rolling your own solution that includes jQuery instead?</p>