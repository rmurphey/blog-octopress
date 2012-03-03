--- 
layout: post
date: "2010-03-10"
title: Building a standalone JavaScriptMVC controller.js
comments: true
categories: howto, javascript, javascriptmvc, jquery
---

I've been doing a lot of work lately with a client who's using just the controller portion of <a href="http://javascriptmvc.com">JavaScriptMVC</a>. I plan to write a more in-depth post about this while I'm traveling this week, but in the meantime, I wanted to jot down the steps to create the standalone controller.js file for my future reference. These instructions work for me on OSX 10.6.

<ol>
	<li>Go to the <a href="http://github.com/pinhook/framework/downloads">JavaScriptMVC downloads page on Github</a>
</li>
	<li>Download the latest version (3.0.0a0 as of this writing) of the framework and unzip it</li>
	<li>Open a terminal window and cd to the directory created when you unzipped the file</li>
	<li>Run <code>steal/js steal/compress/plugin.js jquery/controller</code>
</li>
	<li>You should now have a new controller.js file in the directory; you're all set!</li>
</ol>