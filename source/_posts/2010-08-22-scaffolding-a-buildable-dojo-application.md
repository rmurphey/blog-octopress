--- 
layout: post
date: "2010-08-22"
title: Scaffolding a Buildable Dojo Application
comments: true
categories: dojo package-management build-system dependency-management
---

<p><em>Note: I&rsquo;ve created a <a href="http://github.com/rmurphey/dojo-scaffold">GitHub repo</a> that served as a sort of scratch pad for this article; you may want to clone it and follow along. If you do, though, please note that it does NOT include the Dojo downloads; you&rsquo;ll need to download the files indicated in Step 1 and then place the extracted directories as indicated in Step 2 if you want any of the build stuff to work.</em></p>

<p>One of the most confusing things about using a toolkit like Dojo can be getting it set up properly to take advantage of all of its features, such as its package management system. Sure, you can use a <a href="http://dojotoolkit.org/download/">CDN-hosted version</a> of the toolkit, but one of the major reasons I like Dojo is because of the dependency management and build tools it offers, and it&rsquo;s difficult to really take advantage of these when you&rsquo;re only using the library from the CDN.</p>

<p>If you skip the build step, Dojo&rsquo;s modular nature means you&rsquo;ll end up downloading a whole lot of files, and making a whole lot of HTTP requests in the process. It&rsquo;s fine for development and general messing around, or if your project just requires a few files, but for the kinds of projects where I think Dojo really shines, you&rsquo;re going to want to do your own build.</p>

<p>My goal in this article is to show how I set up my files to make this easier. This is by no means the only way to do it, nor even necessarily a best way to do it &mdash; just one way to do it that you can hopefully follow along with to get you started.</p>

<h2>Step 1: Getting Dojo</h2>

<p>I like to download two different Dojo packages:</p>

<ul>
<li>The <a href="http://download.dojotoolkit.org/release-1.5.0/dojo-release-1.5.0.tar.gz">toolkit release (2mb .tar.gz)</a>, which includes compressed versions of all of the Dojo, Dijit, and Dojox modules.</li>
<li>The <a href="http://download.dojotoolkit.org/release-1.5.0/dojo-release-1.5.0-src.tar.gz">SDK release (19mb .tar.gz)</a>, which contains uncompressed, commented versions of all the modules, along with the build tools</li>
</ul>


<p>I generally don&rsquo;t maintain these files in a project&rsquo;s version control system, but I do document that downloading and extracting them is part of setting up a new development instance. Your exact strategy here may vary, but I do encourage at least the JavaScript developers on a project to have both on hand. The former will be used during development; the latter will be used for reference, and to perform the build step.</p>

<h2>Step 2: Where Do I Put It?</h2>

<p>For the sake of this article, let&rsquo;s assume that all of your public, static files are served from a directory <code>www/</code>. Here&rsquo;s how I might structure things from there:</p>

<ul>
<li>www/

<ul>
<li>dojo-toolkit/ (extracted from toolkit release)</li>
<li>dojo-sdk/ (extracted from SDK release)</li>
<li>myApp/</li>
</ul>
</li>
</ul>


<p>Easy enough. Next, we need to point to Dojo in our HTML.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="tag">&lt;script</span> <span class="attribute-name">src</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">dojo-toolkit/dojo/dojo.js</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span><span class="tag">&lt;/script&gt;</span></pre></div>
</div>


<p>At this point, we could start writing JavaScript that uses Dojo anywhere after that script tag, but that wouldn&rsquo;t be very exciting, smart, or sustainable. Instead, let&rsquo;s create an initial JavaScript file in our <code>myApp/</code> directory, called base.js.</p>

<div class="CodeRay">
  <div class="code"><pre>dojo.provide(<span class="string"><span class="delimiter">'</span><span class="content">myApp.base</span><span class="delimiter">'</span></span>);

console.log(<span class="string"><span class="delimiter">'</span><span class="content">you found me!</span><span class="delimiter">'</span></span>);</pre></div>
</div>


<p>We could just include this file in our page using a script tag, but again, not very sustainable &mdash; next thing you know we&rsquo;ll have 30 script tags in our page, and the whole point here was to take advantage of dependency management and building, right? We can tell Dojo to find and load our file for us, but first, we have to give Dojo a hint as to where to look.</p>

<h2>Step 3: Pointing Dojo in the Right Direction</h2>

<p>The one &ldquo;rule&rdquo; imposed upon you by using Dojo&rsquo;s package system is a simple mapping of dot-notation strings to file locations. If you say <code>dojo.require("a.b.c.d.e.f.g")</code>, Dojo will go looking for a file at <code>a/b/c/d/e/f/g.js</code>. By default, Dojo assumes that the directory <code>a/</code> is a sibling of the directory that contains <code>dojo.js</code>, but you can adjust the location of any namespace.</p>

<p>There are <a href="http://dojotoolkit.org/reference-guide/djConfig.html">a few different ways to do this</a>; my personal preference is to declare the global djConfig variable, but any of the ways mentioned at the link above is obviously valid. If you go the djConfig variable route, just be sure to declare the variable <em>before</em> you include <code>dojo.js</code> in your page if you want Dojo to pay attention to it.</p>

<p>Here&rsquo;s what it would look like for our little app:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="tag">&lt;script&gt;</span>
<span class="inline"><span class="keyword">var</span> djConfig = {
  <span class="key">modulePaths</span> : {
    <span class="key"><span class="delimiter">'</span><span class="content">myApp</span><span class="delimiter">'</span></span> : <span class="string"><span class="delimiter">'</span><span class="content">../../myApp</span><span class="delimiter">'</span></span>
  }
};</span>
<span class="tag">&lt;/script&gt;</span></pre></div>
</div>


<p>What&rsquo;s up with the <code>../../</code> stuff? As mentioned, Dojo by default assumes that any namespace, such as our <code>myApp</code> namespace, is a sibling of the <code>dojo/</code> directory by default, just like <code>dijit/</code> and <code>dojox/</code> are. Problem is, our <code>myApp/</code> directory doesn&rsquo;t follow this convention. Namespace locations are specified relative to the location of <code>dojo.js</code> itself &mdash; in our case, <code>dojo.js</code> is located at <code>dojo-toolkit/dojo/dojo.js</code>, and so relative to that, our <code>myApp/</code> directory is exactly where we said it was: at <code>../../myApp</code>.</p>

<p>Why don&rsquo;t we just make the <code>myApp/</code> directory a sibling of the <code>dojo/</code> directory, and skip this whole part? We could, but keeping it external to Dojo itself makes managing those Dojo downloads &mdash; especially within a version control system &mdash; a whole lot easier. This way, we can keep our application code entirely separate from Dojo, and add or remove or upgrade Dojo at will without having to worry about our application files. This makes me happy; if it doesn&rsquo;t make you happy, feel free to put your application code wherever you&rsquo;d like :)</p>

<h2>Step 4: Hooking it Up</h2>

<p>Now that Dojo knows where to find our stuff, we can tell it to start looking. We&rsquo;ve already included Dojo on our page. Now, instead of adding more script tags to our HTML, we can start to take advantage of Dojo&rsquo;s dependency management system:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="tag">&lt;script&gt;</span>
<span class="inline">dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.base</span><span class="delimiter">'</span></span>);</span>
<span class="tag">&lt;/script&gt;</span></pre></div>
</div>


<p>This is the only other script tag you&rsquo;re going to need to add to your page; from here on out, everything can be managed inside the JavaScript itself.</p>

<h2>Step 5: Writing Your App</h2>

<p>This step is mostly up to you. Dojo provides great tools for building large applications &mdash; a solid base library with CSS-based node selection, inheritance, pubsub, effects, events, and various other hotness &mdash; and it also has a great UI toolkit in Dijit, including abstracted base UI functionality in dijit._Widget and dijit._Templated. It provides patterns, but no real prescriptions, so I can&rsquo;t show you how to build a great Dojo app in three paragraphs or less.</p>

<p>However, let&rsquo;s talk a bit more about this dependency management stuff, because by the end of this article, I want you to be able to leverage that dependency management stuff to create production-ready files, regardless of how you build your app.</p>

<p>So, we have our <code>myApp/base.js</code> file, and so far it contains this:</p>

<div class="CodeRay">
  <div class="code"><pre>dojo.provide(<span class="string"><span class="delimiter">'</span><span class="content">myApp.base</span><span class="delimiter">'</span></span>);

console.log(<span class="string"><span class="delimiter">'</span><span class="content">you found me!</span><span class="delimiter">'</span></span>);</pre></div>
</div>


<p>Let&rsquo;s assume our app does more than log to the console:</p>

<div class="CodeRay">
  <div class="code"><pre>dojo.provide(<span class="string"><span class="delimiter">'</span><span class="content">myApp.base</span><span class="delimiter">'</span></span>);

<span class="comment">// specify the file's dependencies</span>
dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Settings</span><span class="delimiter">'</span></span>);
dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Thinger</span><span class="delimiter">'</span></span>);
dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Doohickey</span><span class="delimiter">'</span></span>);

<span class="comment">// run code when the dependencies are met</span>
<span class="comment">// and the DOM is ready</span>
dojo.ready(<span class="keyword">function</span>() {
  <span class="keyword">if</span> (myApp.Settings.isThisThingOn) {
    <span class="keyword">new</span> myApp.Thinger({ <span class="key">things</span> : [ <span class="string"><span class="delimiter">'</span><span class="content">a</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">b</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">c</span><span class="delimiter">'</span></span> ]})
      .placeAt(dojo.body());
    <span class="keyword">new</span> myApp.Doohickey({ <span class="key">awesome</span> : <span class="predefined-constant">true</span> })
      .placeAt(dojo.body());
  }
});</pre></div>
</div>


<p>Now we&rsquo;ve said, in code, that our app requires three other pieces: Settings, Thinger, and Doohickey. Dojo looks at those <code>dojo.require()</code> statements, and decides that it&rsquo;s going to try to fetch <code>myApp/Settings.js</code>, <code>myApp/Thinger.js</code> and <code>myapp/Doohickey.js</code> to satisfy those dependencies. Once it finds them, we can use them.</p>

<p>The details of our application here aren&rsquo;t super-important (in fact they&rsquo;re entirely made-up); what&rsquo;s important is to understand that we&rsquo;ve specified a dependency, and once it is met, some other code runs.</p>

<p>Chances are that some of our modules have some dependencies of their own. Let&rsquo;s take a look at the make-believe contents of <code>myApp/Thinger.js</code>:</p>

<div class="CodeRay">
  <div class="code"><pre>dojo.provide(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Thinger</span><span class="delimiter">'</span></span>);

dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">dijit._Widget</span><span class="delimiter">'</span></span>);
dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">dijit._Templated</span><span class="delimiter">'</span></span>);

dojo.declare(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Thinger</span><span class="delimiter">'</span></span>, [ dijit._Widget, dijit._Templated ], {
  <span class="comment">// template for Thinger</span>
  <span class="key">templateString</span> : <span class="string"><span class="delimiter">'</span><span class="content">&lt;ul&gt;&lt;/ul&gt;</span><span class="delimiter">'</span></span>,

  <span class="comment">// what to do after a new Thinger </span>
  <span class="comment">// is on the page</span>
  <span class="function">postCreate</span> : <span class="keyword">function</span>() {
    <span class="comment">// create a new list item for each item in things</span>
    dojo.forEach(<span class="local-variable">this</span>.things, <span class="keyword">function</span>(thing) {
      dojo.place(<span class="string"><span class="delimiter">'</span><span class="content">&lt;li&gt;</span><span class="delimiter">'</span></span> + thing + <span class="string"><span class="delimiter">'</span><span class="content">&lt;/li&gt;</span><span class="delimiter">'</span></span>, <span class="local-variable">this</span>.domNode, <span class="string"><span class="delimiter">'</span><span class="content">last</span><span class="delimiter">'</span></span>);
    }, <span class="local-variable">this</span>);
  }
});</pre></div>
</div>


<p>Why look! We&rsquo;ve specified yet more dependencies while keeping our code modular and abstracting away common pieces of behavior. Nifty, eh? Meanwhile, our HTML still only has the single <code>dojo.require()</code> statement in it, because all of our dependencies are expressed in our code, where they belong.</p>

<h2>Step 6: Build It</h2>

<p>We&rsquo;ve seen how to express these dependencies, but if you&rsquo;ve been paying attention to Firebug or your development tool of choice, you&rsquo;ve noticed there are still a ton of HTTP requests happening. What, exactly, have we gained here?</p>

<p>Remember how we downloaded that 19mb SDK? Besides the fact that it has uncompressed, commented versions of every single Dojo file, it also includes the build tool, and now we&rsquo;re going to put it to use.</p>

<p>The commands for running a build are, sadly, not something I keep in my brain, so I like to make a tiny shell script in my application directory that looks like this:</p>

<div class="CodeRay">
  <div class="code"><pre>cd ../dojo-sdk/util/buildscripts
./build.sh profileFile=../../../myApp/myApp.profile.js releaseDir=../../../release</pre></div>
</div>


<p>Could this be prettier? Probably, but it does the trick. It puts us in the buildscripts directory, then runs the <code>build.sh</code> file that&rsquo;s there with the provided arguments: the location of the profile file for the build, and the location we&rsquo;d like the build to end up. Both of these locations are relative to the <code>dojo-sdk/util/buildscripts/build.sh</code> file, so keep that in mind if you decided you didn&rsquo;t like how I organized my files and did something different.</p>

<p>Now all we have to do is create that profile file. You can look in <code>dojo-sdk/util/buildscripts/profiles/</code> for some examples, but here&rsquo;s a good start:</p>

<div class="CodeRay">
  <div class="code"><pre>dependencies = {
  <span class="key">stripConsole</span> : <span class="string"><span class="delimiter">'</span><span class="content">all</span><span class="delimiter">'</span></span>,
  <span class="key">action</span> : <span class="string"><span class="delimiter">'</span><span class="content">clean,release</span><span class="delimiter">'</span></span>,
  <span class="key">optimize</span> : <span class="string"><span class="delimiter">'</span><span class="content">shrinksafe</span><span class="delimiter">'</span></span>,
  <span class="key">releaseName</span> : <span class="string"><span class="delimiter">'</span><span class="content">myApp</span><span class="delimiter">'</span></span>,
  <span class="key">localeList</span> : <span class="string"><span class="delimiter">'</span><span class="content">en-us</span><span class="delimiter">'</span></span>,

  <span class="key">layers</span>: [
    {
      <span class="key">name</span>: <span class="string"><span class="delimiter">&quot;</span><span class="content">../myApp/base.js</span><span class="delimiter">&quot;</span></span>,
      <span class="key">resourceName</span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.base</span><span class="delimiter">&quot;</span></span>,
      <span class="key">dependencies</span>: [
        <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.base</span><span class="delimiter">&quot;</span></span>
      ]
    }
  ],

  <span class="key">prefixes</span>: [
    [ <span class="string"><span class="delimiter">&quot;</span><span class="content">dijit</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../dijit</span><span class="delimiter">&quot;</span></span> ],
    [ <span class="string"><span class="delimiter">&quot;</span><span class="content">dojox</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../dojox</span><span class="delimiter">&quot;</span></span> ],
    [ <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../../myApp</span><span class="delimiter">&quot;</span></span> ]
  ]
}</pre></div>
</div>


<p>OK, seriously, what is <em>up</em> with these relative paths? I will tell you right now that the first time I saw this it was a complete mindf*ck, and the only way I can put together a build for a new project in 10 minutes or less is by referring to a profile file for an old project (which is part of the reason I&rsquo;m writing this post). Let&rsquo;s take a look at what&rsquo;s going on here, though:</p>

<ul>
<li>The <code>dependencies</code> object is the top level object describing the profile. Initially, you can set all of the command-line arguments here as members, e.g.: stripConsole to remove any <code>console.*</code> calls in code, a required <code>action</code> parameter, and so on. (Note that we could also specify the releaseDir argument here if we wanted; I tend to keep it in the shell script, but not for a particularly good reason.)</li>
<li>The <code>layers</code> array contains a list of all of the production-ready &ldquo;layers&rdquo; you want the build to create. In this case, we&rsquo;re telling the build to create one layer that contains all of our application code (in a minute we&rsquo;ll look at how to specify multiple layers for serious hotness).

<ul>
<li>The <code>name</code> property of each layer tells the build tool where the created layer should be placed relative to <code>dojo.js</code> in the destination directory.</li>
<li>The <code>resourceName</code> property tells the build tool how we plan to refer to this layer in our code. For the sake of this article, we&rsquo;re going to refer to it in our code exactly how we&rsquo;re already referring to it in our code.</li>
<li>The <code>dependencies</code> property is an array of modules that this layer depends on. In this case, the layer sort of depends on itself, causing the build to physically replace the original myApp/base.js file with the result of all the dependencies.</li>
</ul>
</li>
<li>The <code>prefixes</code> array is sort of like the <code>modulePaths</code> specification in djConfig, which makes sense because the build tool can&rsquo;t see the djConfig settings. It tells the build tool where it&rsquo;s going to find files for a given namespace. Any namespace that you use in your code needs to be specified here. If you aren&rsquo;t using any DojoX modules, feel free to omit the &ldquo;dojox&rdquo; prefix to avoid copying the unused code into your built tree.</li>
</ul>


<p>So! Let&rsquo;s cd into our <code>myApp/</code> directory, where we&rsquo;ve created our little build script and <code>myApp.profile.js</code>, and run that build script.</p>

<p>[ &hellip; about 30 seconds later &hellip; ]</p>

<p>Sweet. Now we have a <code>release/</code> directory next to our <code>myApp/</code> directory, and it looks like this:</p>

<ul>
<li>release

<ul>
<li>myApp

<ul>
<li>dijit</li>
<li>dojo</li>
<li>dojox</li>
<li>myApp</li>
</ul>
</li>
</ul>
</li>
</ul>


<p>We can see exactly what the build did by looking at <code>release/myApp/dojo/build.txt</code> &mdash; it shows that it created a <code>dojo.js</code> file for us, as well as the <code>myApp/base.js</code> file we asked it to create, complete with all of our dependencies.</p>

<p>Now, to use our built files, we need to do two things:</p>

<ul>
<li>Remove our djConfig settings; they&rsquo;re not necessary now that the built <code>myApp/</code> is a sibling of the <code>dojo/</code> directory.</li>
<li>Change the script tag in our HTML that includes <code>dojo.js</code> to point at <code>release/myApp/dojo/dojo.js</code>.</li>
</ul>


<p>We can do this by hand, but once we have this working, we&rsquo;d be smart to do it with some sort of switch in our application that decides whether we&rsquo;re in development or production mode and outputs the right code accordingly.</p>

<h2>Step 7: Building it Better</h2>

<p>Imagine, if you will, that the <code>myApp.Doohickey</code> module is only needed on one page of your application, and that page is used rarely. Not only that, the module has a ton of dependencies that aren&rsquo;t shared by any other modules, and it&rsquo;s seriously stupid to be taking the time to load all that code when it&rsquo;s unlikely it&rsquo;s going to be needed. Our current profile file doesn&rsquo;t make any allowances for this, but two adjustments can separate this functionality from the built files of the core application.</p>

<p>First, let&rsquo;s give the directive in the profile file to build a separate layer for <code>myApp.Doohickey</code>. It won&rsquo;t work without <code>myApp.base</code>, so we specify that as a layer dependency:</p>

<div class="CodeRay">
  <div class="code"><pre>dependencies = {
  <span class="key">stripConsole</span> : <span class="string"><span class="delimiter">'</span><span class="content">all</span><span class="delimiter">'</span></span>,
  <span class="key">action</span> : <span class="string"><span class="delimiter">'</span><span class="content">clean,release</span><span class="delimiter">'</span></span>,
  <span class="key">optimize</span> : <span class="string"><span class="delimiter">'</span><span class="content">shrinksafe</span><span class="delimiter">'</span></span>,
  <span class="key">releaseName</span> : <span class="string"><span class="delimiter">'</span><span class="content">myApp</span><span class="delimiter">'</span></span>,
  <span class="key">localeList</span> : <span class="string"><span class="delimiter">'</span><span class="content">en-us</span><span class="delimiter">'</span></span>,

  <span class="key">layers</span>: [
    {
      <span class="key">name</span>: <span class="string"><span class="delimiter">&quot;</span><span class="content">../myApp/base.js</span><span class="delimiter">&quot;</span></span>,
      <span class="key">resourceName</span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.base</span><span class="delimiter">&quot;</span></span>,
      <span class="key">dependencies</span>: [
        <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.base</span><span class="delimiter">&quot;</span></span>
      ]
    },

    {
      <span class="key">name</span>: <span class="string"><span class="delimiter">&quot;</span><span class="content">../myApp/Doohickey.js</span><span class="delimiter">&quot;</span></span>,
      <span class="key">resourceName</span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.Doohickey</span><span class="delimiter">&quot;</span></span>,
      <span class="key">dependencies</span> : [
        <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp.Doohickey</span><span class="delimiter">&quot;</span></span>
      ],
      <span class="key">layerDependencies</span> : [
        <span class="string"><span class="delimiter">&quot;</span><span class="content">../myApp/base.js</span><span class="delimiter">&quot;</span></span>
      ]
    }
  ],

  <span class="key">prefixes</span>: [
          [ <span class="string"><span class="delimiter">&quot;</span><span class="content">dijit</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../dijit</span><span class="delimiter">&quot;</span></span> ],
          [ <span class="string"><span class="delimiter">&quot;</span><span class="content">dojox</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../dojox</span><span class="delimiter">&quot;</span></span> ],
          [ <span class="string"><span class="delimiter">&quot;</span><span class="content">myApp</span><span class="delimiter">&quot;</span></span>, <span class="string"><span class="delimiter">&quot;</span><span class="content">../../myApp</span><span class="delimiter">&quot;</span></span> ]
  ]
}</pre></div>
</div>


<p>Second, we need to make sure the build tool doesn&rsquo;t see <code>dojo.require('myApp.Doohickey')</code> and decide it needs to include it in the base layer. This may seem a little weird, but the build tool basically just parses files to figure out which other files they require, and it does this by looking for <code>dojo.require()</code> statements. Hiding the Doohickey requirement from the build tool while keeping it in place for development is easy:</p>

<div class="CodeRay">
  <div class="code"><pre>dojo.provide(<span class="string"><span class="delimiter">'</span><span class="content">myApp.base</span><span class="delimiter">'</span></span>);

dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Settings</span><span class="delimiter">'</span></span>);
dojo.require(<span class="string"><span class="delimiter">'</span><span class="content">myApp.Thinger</span><span class="delimiter">'</span></span>);

dojo.addOnLoad(<span class="keyword">function</span>() {
  <span class="keyword">new</span> myApp.Thinger({ <span class="key">things</span> : [ <span class="string"><span class="delimiter">'</span><span class="content">a</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">b</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">c</span><span class="delimiter">'</span></span> ]})
    .placeAt(dojo.body());
});

<span class="keyword">if</span> (pageNeedsDoohickey) {
  dojo[<span class="string"><span class="delimiter">&quot;</span><span class="content">require</span><span class="delimiter">&quot;</span></span>](<span class="string"><span class="delimiter">'</span><span class="content">myApp.Doohickey</span><span class="delimiter">'</span></span>);
  dojo.addOnLoad(<span class="keyword">function</span>() {
    <span class="keyword">new</span> myApp.Doohickey({ <span class="key">awesome</span> : <span class="predefined-constant">true</span> })
      .placeAt(dojo.body());
  });
}</pre></div>
</div>


<h2>Step 8</h2>

<p>There is no step 8. There is step 1 through 7, which is way more than &ldquo;put this script tag (and 20 others!) on your page and GO&rdquo;, I&rsquo;ll give you that. But take heart: not long ago, these steps had me pulling my hair out, and now I can write a blog post about them (with some much-appreciated review from <a href="http://kennethfranqueiro.com/">Ken</a> and <a href="http://higginsforpresident.net">Pete</a>). I hope they help you understand the steps you&rsquo;d take to start taking advantage of the power of Dojo&rsquo;s package system, and I promise that once you&rsquo;ve done it once or twice, it won&rsquo;t seem like the roadblock it does at first, especially because the payoff in the long run is huge.</p>

<h2>Further Reading</h2>

<ul>
<li><a href="http://docs.dojocampus.org/build/index">Documentation of the Dojo build system</a></li>
<li><a href="http://docs.dojocampus.org/dojo/cache">dojo.cache</a>: Using module paths to specify the location of strings such as templates, which are then interned into your code at build time: <code>dojo.cache('myApp.templates', 'Thinger.html)</code></li>
<li><a href="http://github.com/rmurphey/dojo-scaffold">A GitHub repo of my scratch pad for this article</a></li>
</ul>