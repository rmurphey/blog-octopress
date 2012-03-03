--- 
layout: post
date: "2009-10-15"
title: Using Objects to Organize Your Code
comments: true
categories: howto, jquery
---

<p>This is a reprint of an article that originally appeared in the March 2009 issue of <a href="http://jsmag.com">JSMag</a>.</p>

<p>When you move beyond simple snippets of jQuery and start developing more complex user interactions, your code can quickly become unwieldy and difficult to debug. This article shows you how to start thinking about these interactions in terms of the bits of behavior the feature comprises, using the object literal pattern.</p>

<p>In the past few years, JavaScript libraries have given beginning developers the ability to add elaborate interactions to their sites. Some, like jQuery, have a syntax so simple that people with zero programming experience can quickly add bells and whistles to their pages.  Adding all those bells and whistles, even some pretty elaborate ones, seems to be just a few Google searches away. A copy here, a paste there, a plugin or a few dozen lines of custom code — the client is duly impressed, and you’re adding jQuery to your resume.</p>

<p>But wait. Now the requirements have changed. Now the thing that needed to work for three elements needs to work for ten. Now your code needs to be reused for a slightly different application where all the IDs are different.  We’ve all seen the snippets that make jQuery (and other libraries) look dead-simple. What those snippets leave out — and hey, they’re just snippets, right? — is how to design your code when your needs go beyond dropping in a plugin or doing some <code>show()</code> and <code>hide()</code>.</p>

<h2>Introducing the Object Literal pattern</h2>

<p>The object literal pattern offers a way to organize code by the behaviors it comprises. It’s also a means to keep your code from “polluting the global namespace,” which is a good practice for all projects and imperative for larger ones. It forces you to think at the outset about what your code will do and what pieces need to be in place in order for you to do it. An object literal is a way to encapsulate related behaviors, as shown here:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> myObjectLiteral = {
    <span class="function">myBehavior1</span> : <span class="keyword">function</span>() {
        <span class="comment">/* do something */</span>
    },

    <span class="function">myBehavior2</span> : <span class="keyword">function</span>() {
        <span class="comment">/* do something else */</span>
    }
};</pre></div>
</div>


<p>As an artificially simplistic example, suppose you had the jQuery shown in Listing 2 for showing and hiding content when a list item was clicked.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="predefined">$</span>(document).ready(<span class="keyword">function</span>() {
    <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#myFeature li</span><span class="delimiter">'</span></span>)
        .append(<span class="string"><span class="delimiter">'</span><span class="content">&lt;div/&gt;</span><span class="delimiter">'</span></span>)
        .each(<span class="keyword">function</span>() {
            <span class="predefined">$</span>(<span class="local-variable">this</span>).find(<span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>)
                .load(<span class="string"><span class="delimiter">'</span><span class="content">foo.php?item=</span><span class="delimiter">'</span></span> + <span class="predefined">$</span>(<span class="local-variable">this</span>).attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>));
        })
    .click(<span class="keyword">function</span>() {
        <span class="predefined">$</span>(<span class="local-variable">this</span>).find(<span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>).show();
        <span class="predefined">$</span>(<span class="local-variable">this</span>).siblings().find(<span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>).hide();
    });
});</pre></div>
</div>


<p>Simple enough, and yet even in this example there are several things you might want to change later — for example, the way you determine the URL for loading the content, the destination of the loaded content, or the show and hide behavior.  An object literal representation of the feature cleanly separates these aspects. It might look like this:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> myFeature = {
    <span class="key">config</span> : {
        <span class="key">wrapper</span> : <span class="string"><span class="delimiter">'</span><span class="content">#myFeature</span><span class="delimiter">'</span></span>,
        <span class="key">container</span> : <span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>,
        <span class="key">urlBase</span> : <span class="string"><span class="delimiter">'</span><span class="content">foo.php?item=</span><span class="delimiter">'</span></span>
    },

    <span class="function">init</span> : <span class="keyword">function</span>(config) {
        <span class="predefined">$</span>.extend(myFeature.config, config);
        <span class="predefined">$</span>(myFeature.config.wrapper).find(<span class="string"><span class="delimiter">'</span><span class="content">li</span><span class="delimiter">'</span></span>).
            each(<span class="keyword">function</span>() {
                myFeature.getContent(<span class="predefined">$</span>(<span class="local-variable">this</span>));
            }).
            click(<span class="keyword">function</span>() {
                myFeature.showContent(<span class="predefined">$</span>(<span class="local-variable">this</span>));
            });
    },

    <span class="function">buildUrl</span> : <span class="keyword">function</span>(<span class="predefined">$li</span>) {
        <span class="keyword">return</span> myFeature.config.urlBase + <span class="predefined">$li</span>.attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>);
    },

    <span class="function">getContent</span> : <span class="keyword">function</span>(<span class="predefined">$li</span>) {
        <span class="predefined">$li</span>.append(myFeature.config.container);
        <span class="keyword">var</span> url = myFeature.buildUrl(<span class="predefined">$li</span>);
        <span class="predefined">$li</span>.find(myFeature.config.container).load(url);
    },

    <span class="function">showContent</span> : <span class="keyword">function</span>(<span class="predefined">$li</span>) {
        <span class="predefined">$li</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>).show();
        myFeature.hideContent(<span class="predefined">$li</span>.siblings());
    },

    <span class="function">hideContent</span> : <span class="keyword">function</span>(<span class="predefined">$elements</span>) {
        <span class="predefined">$elements</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">div</span><span class="delimiter">'</span></span>).hide();
    }
};

<span class="predefined">$</span>(document).ready(<span class="keyword">function</span>() { myFeature.init(); });</pre></div>
</div>


<p>Because the initial example was incredibly simplistic, the object literal incarnation is longer. Truth be told, the object literal method generally won’t save you lines of code. What it will save is headaches. By using an object literal, we’ve broken our code into its logical parts, making it easy to locate the things we might want to change down the road. We’ve made our feature extendable, by providing the ability to pass in overrides to the default configuration. And, we’ve done some limited self-documentation — it’s easy to see at a glance what the feature does. As your needs grow beyond the simplicity of this example the benefits of the structure will become clearer, as you’ll see below.</p>

<p>_Note: For an excellent primer on objects, properties, and methods, check out <a href="http://www.amazon.com/Object-Oriented-JavaScript-high-quality-applications-libraries/dp/1847194141">Object-Oriented JavaScript: Create scalable, reusable high-quality JavaScript applications and libraries</a> by Stoyan Stefanov. You may also want to read up on JSON (JavaScript Object Notation).</p>

<h2>An in-depth example</h2>

<p>Our mission will be to create a UI element that features multiple pieces of content divided into several sections. Clicking on a section will show a list of items in the section; clicking on an item in the left nav will show the item in the content area. Whenever a section is shown, the first item in the section should be shown. The first section should be shown when the page loads.</p>

<h3>Step 1: Crafting the HTML</h3>

<p>Writing good semantic HTML is a crucial prerequisite to writing good JavaScript, so let’s start by thinking about what the HTML for something like this might look like. The HTML should:</p>

<ul>
<li>Make sense (and work) when JavaScript isn’t available.</li>
<li>Provide a predictable DOM to which we can attach JavaScript.</li>
<li>Avoid unnecessary IDs and classes (and you might be surprised by how few are necessary).</li>
</ul>


<p>With those guidelines in mind, we’ll start with <a href="http://www.rebeccamurphey.com/jsmag/object-literal/">this html</a>.  Note that we haven’t included any markup to display the section navigation or the item navigation; those pieces will be added by jQuery since they will only work with jQuery; non-JavaScript users will get nice semantic markup. (If there’s anything surprising or confusing in that HTML, now would be a good time to read up on POSH (plain-old semantic HTML) and progressive enhancement.)</p>

<h3>Step 2: Scaffolding the Object</h3>

<p>My first step in creating an object for a feature is to create “stubs” within the object. Stubs are basically placeholders; they’re the outline for the feature we’re going to build. Our object will have the following methods:</p>

<ul>
<li><code>myFeature.init()</code> will run on <code>$(document).ready()</code>. It will turn the semantic HTML we start with into a JavaScript-enabled user interface.</li>
<li><code>myFeature.buildSectionNav()</code> will be called by <code>myFeature.init()</code>. It will take a jQuery object that contains all of the sections from the semantic HTML and use those sections to build the top navigation. It will bind the click handlers to the top navigation items so that clicking on them will show the appropriate section.</li>
<li><code>myFeature.buildItemNav()</code> will be called by <code>myFeature.showSection()</code>. It will ake a jQuery object that contains all of the items associated with the section from the semantic HTML, and use them to build the side navigation. It will bind the click handlers to the side navigation items so that clicking on them will show the appropriate content.</li>
<li><code>myFeature.showSection()</code> will be called when the user clicks on an item in the top navigation. It will use the navigation item that’s clicked on to figure out which section to show from the semantic HTML.</li>
<li><code>myFeature.showContentItem()</code> will be called when the user clicks on an item in the side navigation. It will use the navigation item that’s clicked on to figure out which content item to show from the semantic HTML.</li>
</ul>


<p>We’ll also make room for a configuration property, <code>myFeature.config</code>, which will be a single location for setting default values rather than scattering them throughout the code. We’ll include the ability to override the defaults when we define the <code>myFeature.init()</code> method.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> myFeature = {
    <span class="key"><span class="delimiter">'</span><span class="content">config</span><span class="delimiter">'</span></span> : { },
    <span class="key"><span class="delimiter">'</span><span class="content">init</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() { },
    <span class="key"><span class="delimiter">'</span><span class="content">buildSectionNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() { },
    <span class="key"><span class="delimiter">'</span><span class="content">buildItemNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() { },
    <span class="key"><span class="delimiter">'</span><span class="content">showSection</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() { },
    <span class="key"><span class="delimiter">'</span><span class="content">showContentItem</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() { }
};</pre></div>
</div>


<h3>Step 3: The Code</h3>

<p>Once we’ve built this skeleton, it’s time to start coding. Let’s start by setting up a simple <code>myFeature.config</code> object and writing the <code>myFeature.init()</code> method:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="string"><span class="delimiter">'</span><span class="content">config</span><span class="delimiter">'</span></span> : {
    <span class="comment">// default container is #myFeature</span>
    <span class="key"><span class="delimiter">'</span><span class="content">container</span><span class="delimiter">'</span></span> : <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#myFeature</span><span class="delimiter">'</span></span>)
},

<span class="key"><span class="delimiter">'</span><span class="content">init</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(config) {
    <span class="comment">// provide for custom configuration via init()</span>
    <span class="keyword">if</span> (config &amp;&amp; <span class="keyword">typeof</span>(config) == <span class="string"><span class="delimiter">'</span><span class="content">object</span><span class="delimiter">'</span></span>) {
        <span class="predefined">$</span>.extend(myFeature.config, config);
    }

    <span class="comment">// create and/or cache some DOM elements </span>
    <span class="comment">// we'll want to use throughout the code</span>
    myFeature.<span class="predefined">$container</span> = myFeature.config.container;

    myFeature.<span class="predefined">$sections</span> = myFeature.<span class="predefined">$container</span>.
        <span class="comment">// only select immediate children!</span>
        find(<span class="string"><span class="delimiter">'</span><span class="content">ul.sections &gt; li</span><span class="delimiter">'</span></span>);

    myFeature.<span class="predefined">$section_nav</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;p/&gt;</span><span class="delimiter">'</span></span>)
      .attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>,<span class="string"><span class="delimiter">'</span><span class="content">section_nav</span><span class="delimiter">'</span></span>)
      .prependTo(myFeature.<span class="predefined">$container</span>);

    myFeature.<span class="predefined">$item_nav</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;p/&gt;</span><span class="delimiter">'</span></span>)
      .attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>,<span class="string"><span class="delimiter">'</span><span class="content">item_nav</span><span class="delimiter">'</span></span>)
      .insertAfter(myFeature.<span class="predefined">$section_nav</span>);

    myFeature.<span class="predefined">$content</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;p/&gt;</span><span class="delimiter">'</span></span>)
      .attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>,<span class="string"><span class="delimiter">'</span><span class="content">content</span><span class="delimiter">'</span></span>)
      . insertAfter(myFeature.<span class="predefined">$item_nav</span>);

  <span class="comment">// build the section-level nav and    </span>
  <span class="comment">// &quot;click&quot; the first item</span>
  myFeature.buildSectionNav(myFeature.<span class="predefined">$sections</span>);
  myFeature.<span class="predefined">$section_nav</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">li:first</span><span class="delimiter">'</span></span>).click();

  <span class="comment">// hide the plain HTML from sight</span>
  myFeature.<span class="predefined">$container</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">ul.sections</span><span class="delimiter">'</span></span>).hide();

  <span class="comment">// make a note that the initialization    </span>
  <span class="comment">// is complete; we don't strictly need this   </span>
  <span class="comment">// for this iteration, but it can come in handy   </span>
  myFeature.initialized = <span class="predefined-constant">true</span>;
}</pre></div>
</div>


<p>Next we’ll create the <code>myFeature.buildSectionNav()</code> method:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="string"><span class="delimiter">'</span><span class="content">buildSectionNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$sections</span>) {

    <span class="comment">// iterate over the provided list of sections</span>
    <span class="predefined">$sections</span>.each(<span class="keyword">function</span>() {

        <span class="comment">// get the section</span>
        <span class="keyword">var</span> <span class="predefined">$section</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>);

        <span class="comment">// create a list item for the section navigation</span>
        <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;li/&gt;</span><span class="delimiter">'</span></span>)
          <span class="comment">// use the text of the first h2</span>
          <span class="comment">// in the section as the text for</span>
          <span class="comment">// the section navigation</span>
          .text(<span class="predefined">$section</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">h2:first</span><span class="delimiter">'</span></span>).text())

          <span class="comment">// add the list item to the section navigation</span>
          .appendTo(myFeature.<span class="predefined">$section_nav</span>)

          <span class="comment">// use data() to store a reference</span>
          <span class="comment">// to the original section on the</span>
          <span class="comment">// newly-created list item</span>
          .data(<span class="string"><span class="delimiter">'</span><span class="content">section</span><span class="delimiter">'</span></span>, <span class="predefined">$section</span>)

          <span class="comment">// bind the click behavior</span>
          <span class="comment">// to the newly created list itme</span>
          <span class="comment">// so it will show the section</span>
          .click(myFeature.showSection);
});</pre></div>
</div>


<p>Next we’ll create the <code>myFeature.buildItemNav()</code> method:</p>

<p>&lsquo;buildItemNav&rsquo; : function($items) {</p>

<div class="CodeRay">
  <div class="code"><pre>// iterate over the provided list of items
$items.each(function() {

    // get the item
    var $item = $(this);

    // create a list item element for the
    // item navigation
    $('&lt;li&gt;')

      // use the text of the first h3
      // in the item as the text for the
      // item navigation
      .text($item.find('h3:first').text())

      // add the list item to the item navigation
      .appendTo(myFeature.$item.nav)

      // use data to store a reference
      // to the original item on the
      // newly created list item
      .data('item', $item)

      // bind the click behavior to the
      // newly created list item so it will
      // show the content item
      .click(myFeature.showContentItem);

});</pre></div>
</div>


<p>Finally, we’ll write the methods for showing sections and content items:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="string"><span class="delimiter">'</span><span class="content">showSection</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {
    <span class="comment">// capture the list item that was clicked on</span>
  <span class="keyword">var</span> <span class="predefined">$li</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>);

  <span class="comment">// clear out the left nav and content area</span>
  myFeature.<span class="predefined">$item_nav</span>.empty();
  myFeature.<span class="predefined">$content</span>.empty();

  <span class="comment">// get the jQuery section object from the orginal HTML,</span>
  <span class="comment">// which we stored using data() during buildSectionNav</span>
  <span class="keyword">var</span> <span class="predefined">$section</span> = <span class="predefined">$li</span>.data(<span class="string"><span class="delimiter">'</span><span class="content">section</span><span class="delimiter">'</span></span>);

  <span class="comment">// mark the clicked list item as current</span>
  <span class="comment">// and remove the current marker from its siblings</span>
  <span class="predefined">$li</span>.addClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>)
    .siblings().removeClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>);

  <span class="comment">// find all of the items related to the section</span>
  <span class="keyword">var</span> <span class="predefined">$items</span> = <span class="predefined">$section</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">ul li</span><span class="delimiter">'</span></span>);

  <span class="comment">// build the item nav for the section</span>
  myFeature.buildItemNav(<span class="predefined">$items</span>);

  <span class="comment">// &quot;click&quot; on the first list item in the section's item nav</span>
  myFeature.<span class="predefined">$item_nav</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">li:first</span><span class="delimiter">'</span></span>).click();

},

<span class="key"><span class="delimiter">'</span><span class="content">showContentItem</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {
  <span class="keyword">var</span> <span class="predefined">$li</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>);

  <span class="comment">// mark the clicked list item as current</span>
  <span class="comment">// and revmoe the current marker from its siblings</span>
  <span class="predefined">$li</span>.addClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>)
    .siblings().removeClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>);

  <span class="comment">// get the jQuery item object from the original HTML,</span>
  <span class="comment">// which we stored using data during buildContentNav</span>
  <span class="keyword">var</span> <span class="predefined">$item</span> = <span class="predefined">$li</span>.data(<span class="string"><span class="delimiter">'</span><span class="content">item</span><span class="delimiter">'</span></span>);

  <span class="comment">// use the item's HTML to populate the content area</span>
  myFeature.<span class="predefined">$content</span>.html(<span class="predefined">$item</span>.html());
}</pre></div>
</div>


<p>All that’s left to do is to call the myFeature.init() method:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="predefined">$</span>(document).ready(myFeature.init);</pre></div>
</div>


<p>You can see the whole thing (including a little CSS to make it presentable) <a href="http://www.rebeccamurphey.com/jsmag/object-literal/">here</a>.</p>

<h3>Step 4: Changing Requirements</h3>

<p>No project is complete without some last-minute change in the requirements, right? Here’s where the object literal approach really shines by making it quick and fairly painless to implement last-minute changes.  What if we need to get the content item excerpts via AJAX instead of from the HTML? Assuming the backend is set up to handle it, try this:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> myFeature = {

    <span class="key"><span class="delimiter">'</span><span class="content">config</span><span class="delimiter">'</span></span> : {
        <span class="key"><span class="delimiter">'</span><span class="content">container</span><span class="delimiter">'</span></span> : <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#myFeature</span><span class="delimiter">'</span></span>),

        <span class="comment">// configurable function for getting</span>
        <span class="comment">// a URL for loading item content</span>
        <span class="key"><span class="delimiter">'</span><span class="content">getItemURL</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$item</span>) {
            <span class="keyword">return</span> <span class="predefined">$item</span>.find(<span class="string"><span class="delimiter">'</span><span class="content">a:first</span><span class="delimiter">'</span></span>).attr(<span class="string"><span class="delimiter">'</span><span class="content">href</span><span class="delimiter">'</span></span>);
        }

    },

    <span class="key"><span class="delimiter">'</span><span class="content">init</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(config) {
        <span class="comment">// stays the same   </span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">buildSectionNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$sections</span>) {
        <span class="comment">// stays the same   </span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">buildItemNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$items</span>) {
        <span class="comment">// stays the same   </span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">showSection</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {
        <span class="comment">// stays the same</span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">showContentItem</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {

        <span class="keyword">var</span> <span class="predefined">$li</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>);

        <span class="predefined">$li</span>.addClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>).
            siblings().removeClass(<span class="string"><span class="delimiter">'</span><span class="content">current</span><span class="delimiter">'</span></span>);

        <span class="keyword">var</span> <span class="predefined">$item</span> = <span class="predefined">$li</span>.data(<span class="string"><span class="delimiter">'</span><span class="content">item</span><span class="delimiter">'</span></span>);
        <span class="keyword">var</span> url = myFeature.config.getItemURL(<span class="predefined">$item</span>);

        <span class="comment">// myFeature.$content.html($item.html());</span>
        myFeature.<span class="predefined">$content</span>.load(url);

    }

};</pre></div>
</div>


<p>Do you need more flexibility? There’s a lot more you can configure (and therefore override) if you really want to make this flexible. For example, you can use myFeature.config to specify how to find and process the title text for each item in the left nav.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> myFeature = {
    <span class="key"><span class="delimiter">'</span><span class="content">config</span><span class="delimiter">'</span></span> : {
        <span class="key"><span class="delimiter">'</span><span class="content">container</span><span class="delimiter">'</span></span> : <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#myFeature</span><span class="delimiter">'</span></span>),

        <span class="comment">// specify the default selector</span>
        <span class="comment">// for finding the text to use</span>
        <span class="comment">// for each item in the item nav</span>
        <span class="key"><span class="delimiter">'</span><span class="content">itemNavSelector</span><span class="delimiter">'</span></span> : <span class="string"><span class="delimiter">'</span><span class="content">h3</span><span class="delimiter">'</span></span>,

        <span class="comment">// specify a default callback </span>
        <span class="comment">// for &quot;processing&quot; the jQuery object</span>
        <span class="comment">// returned by the itemNavText selector</span>
        <span class="key"><span class="delimiter">'</span><span class="content">itemNavProcessor</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$selection</span>) {
            <span class="keyword">return</span> <span class="string"><span class="delimiter">'</span><span class="content">Preview of </span><span class="delimiter">'</span></span> +
                <span class="predefined">$selection</span>.eq(<span class="integer">0</span>).text();
        }
    },

    <span class="key"><span class="delimiter">'</span><span class="content">init</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(config) {
        <span class="comment">// stays the same</span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">buildSectionNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$sections</span>) {
        <span class="comment">// stays the same</span>
    },

    <span class="key"><span class="delimiter">'</span><span class="content">buildItemNav</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>(<span class="predefined">$items</span>) {

        <span class="predefined">$items</span>.each(<span class="keyword">function</span>() {
            <span class="keyword">var</span> <span class="predefined">$item</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>);

            <span class="comment">// use the selector and processor </span>
            <span class="comment">// from the config</span>
            <span class="comment">// to get the text for each item nav</span>
            <span class="keyword">var</span> myText = myFeature.config.itemNavProcessor(
                <span class="predefined">$item</span>.find(myFeature.config.itemNavSelector)
            );

            <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;li/&gt;</span><span class="delimiter">'</span></span>)
            <span class="comment">// use the new variable                 </span>
            <span class="comment">// as the text for the nav item                 </span>
              .text(myText)
              .appendTo(myFeature.<span class="predefined">$item_nav</span>)
              .data(<span class="string"><span class="delimiter">'</span><span class="content">item</span><span class="delimiter">'</span></span>, <span class="predefined">$item</span>)
              .click(myFeature.showContentItem);
      });
  },

  <span class="key"><span class="delimiter">'</span><span class="content">showSection</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {
    <span class="comment">// stays the same   </span>
  },

  <span class="key"><span class="delimiter">'</span><span class="content">showContentItem</span><span class="delimiter">'</span></span> : <span class="keyword">function</span>() {
    <span class="comment">// stays the same   </span>
  }

};</pre></div>
</div>


<p>Once you’ve added defaults to the config object, you can override them when you call <code>myFeature.init()</code>:</p>

<div class="CodeRay">
  <div class="code"><pre>$(document).ready(function() {
    myFeature.init({ 'itemNavSelector' : 'h2' });
});</pre></div>
</div>


<p>Beyond the scope of this article (but also interesting to contemplate and much easier with the object literal pattern) is this: making the back button retrace your path through the tabs using the jQuery history plugin. I leave it as an exercise for the reader.</p>

<h2>Conclusion</h2>

<p>If you’ve stepped through the code examples in this column, you should have a basic understanding of the object literal pattern and how it might prove useful to you as you develop more complex features and interactions. You also have access to some code that you can use to build on this basic foundation.</p>

<p>I encourage you to give this pattern a try the next time you find yourself writing more than a few lines of JavaScript — it forces you to think through the elements and behaviors that make up a complex feature or interaction. Once you become proficient, it provides a sturdy foundation for extending and reusing your code.</p>

<h2>Learn More</h2>

<ul>
<li><a href="http://docs.jquery.com/Data">More on the jQuery <code>data()</code> method</a></li>
<li><a href="http://www.wait-till-i.com/2006/02/16/show-love-to-the-object-literal/">More praise for the object literal pattern</a></li>
<li><a href="http://www.mikage.to/jquery/jquery_history.html">The jQuery History plugin</a></li>
<li><a href="http://paulirish.com/2009/markup-based-unobtrusive-comprehensive-dom-ready-execution/">An interesting application of the object literal pattern for architecting code for multiple page types</a></li>
<li><a href="http://www.slideshare.net/rmurphey/using-objects-to-organize-your-jquery-code">My presentation at the 2009 jQuery Conference about code organization</a></li>
</ul>