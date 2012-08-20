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

{% codeblock lang:javascript %}
var myObjectLiteral = {
    myBehavior1 : function() {
        /* do something */
    },

    myBehavior2 : function() {
        /* do something else */
    }
};
{% endcodeblock %}


<p>As an artificially simplistic example, suppose you had the jQuery shown in Listing 2 for showing and hiding content when a list item was clicked.</p>

{% codeblock lang:javascript %}
$(document).ready(function() {
    $('#myFeature li')
        .append('<div/>')
        .each(function() {
            $(this).find('div')
                .load('foo.php?item=' + $(this).attr('id'));
        })
    .click(function() {
        $(this).find('div').show();
        $(this).siblings().find('div').hide();
    });
});
{% endcodeblock %}

<p>Simple enough, and yet even in this example there are several things you might want to change later — for example, the way you determine the URL for loading the content, the destination of the loaded content, or the show and hide behavior.  An object literal representation of the feature cleanly separates these aspects. It might look like this:</p>

{% codeblock lang:javascript %}
var myFeature = {
    config : {
        wrapper : '#myFeature',
        container : 'div',
        urlBase : 'foo.php?item='
    },

    init : function(config) {
        $.extend(myFeature.config, config);
        $(myFeature.config.wrapper).find('li').
            each(function() {
                myFeature.getContent($(this));
            }).
            click(function() {
                myFeature.showContent($(this));
            });
    },

    buildUrl : function($li) {
        return myFeature.config.urlBase + $li.attr('id');
    },

    getContent : function($li) {
        $li.append(myFeature.config.container);
        var url = myFeature.buildUrl($li);
        $li.find(myFeature.config.container).load(url);
    },

    showContent : function($li) {
        $li.find('div').show();
        myFeature.hideContent($li.siblings());
    },

    hideContent : function($elements) {
        $elements.find('div').hide();
    }
};

$(document).ready(function() { myFeature.init(); });
{% endcodeblock %}


<p>Because the initial example was incredibly simplistic, the object literal incarnation is longer. Truth be told, the object literal method generally won’t save you lines of code. What it will save is headaches. By using an object literal, we’ve broken our code into its logical parts, making it easy to locate the things we might want to change down the road. We’ve made our feature extendable, by providing the ability to pass in overrides to the default configuration. And, we’ve done some limited self-documentation — it’s easy to see at a glance what the feature does. As your needs grow beyond the simplicity of this example the benefits of the structure will become clearer, as you’ll see below.</p>

<p>_Note: For an excellent primer on objects, properties, and methods, check out <a href="http://www.amazon.com/Object-Oriented-JavaScript-high-quality-applications-libraries/dp/1847194141">Object-Oriented JavaScript: Create scalable, reusable high-quality JavaScript applications and libraries</a> by Stoyan Stefanov. You may also want to read up on JSON (JavaScript Object Notation)._</p>

<h2>An in-depth example</h2>

<p>Our mission will be to create a UI element that features multiple pieces of content divided into several sections. Clicking on a section will show a list of items in the section; clicking on an item in the left nav will show the item in the content area. Whenever a section is shown, the first item in the section should be shown. The first section should be shown when the page loads.</p>

<h3>Step 1: Crafting the HTML</h3>

<p>Writing good semantic HTML is a crucial prerequisite to writing good JavaScript, so let’s start by thinking about what the HTML for something like this might look like. The HTML should:</p>

<ul>
<li>Make sense (and work) when JavaScript isn’t available.</li>
<li>Provide a predictable DOM to which we can attach JavaScript.</li>
<li>Avoid unnecessary IDs and classes (and you might be surprised by how few are necessary).</li>
</ul>


With those guidelines in mind, we’ll start with this html.

{% codeblock lang:html %}

<h1>This is My Nifty Feature</h1>

<div id="myFeature">
  <ul class="sections">
    <li>
      <h2><a href="/section/1">Section 1</a></h2>
      <ul>
        <li>
          <h3><a href="/section/1/content/1">Section 1 Title 1</a></h3>
          <p>The excerpt content for Content Item 1</p>
        </li>
        <li>
          <h3><a href="/section/1/content/2">Section 1 Title 2</a></h3>
          <p>The excerpt content for Content Item 2</p>
        </li>
        <li>
          <h3><a href="/section/1/content/3">Section 1 Title 3</a></h3>
          <p>The excerpt content for Content Item 3</p>
        </li>
      </ul>
    </li>

    <li>
      <h2><a href="/section/2">Section 2</a></h2>
      <ul>
        <li>
          <h3><a href="/section/2/content/1">Section 2 Title 1</a></h3>
          <p>The excerpt content for Content Item 1</p>
        </li>
        <li>
          <h3><a href="/section/2/content/2">Section 2 Title 2</a></h3>
          <p>The excerpt content for Content Item 2</p>
        </li>
        <li>
          <h3><a href="/section/2/content/3">Section 2 Title 3</a></h3>
          <p>The excerpt content for Content Item 3</p>
        </li>
      </ul>
    </li>

    <li>
      <h2><a href="/section/3">Section 3</a></h2>
      <ul>
        <li>
          <h3><a href="/section/3/content/1">Section 3 Title 1</a></h3>
          <p>The excerpt content for Content Item 1</p>
        </li>
        <li>
          <h3><a href="/section/3/content/2">Section 3 Title 2</a></h3>
          <p>The excerpt content for Content Item 2</p>
        </li>
        <li>
          <h3><a href="/section/3/content/3">Section 3 Title 3</a></h3>
          <p>The excerpt content for Content Item 3</p>
        </li>
      </ul>
    </li>

  </ul>
</div>
{% endcodeblock %}


Note that we haven’t included any markup to display the section navigation or the item navigation; those pieces will be added by jQuery since they will only work with jQuery; non-JavaScript users will get nice semantic markup. (If there’s anything surprising or confusing in that HTML, now would be a good time to read up on POSH (plain-old semantic HTML) and progressive enhancement.)</p>

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

{% codeblock lang:javascript %}
var myFeature = {
    'config' : { },
    'init' : function() { },
    'buildSectionNav' : function() { },
    'buildItemNav' : function() { },
    'showSection' : function() { },
    'showContentItem' : function() { }
};
{% endcodeblock %}

<h3>Step 3: The Code</h3>

<p>Once we’ve built this skeleton, it’s time to start coding. Let’s start by setting up a simple <code>myFeature.config</code> object and writing the <code>myFeature.init()</code> method:</p>


{% codeblock lang:javascript %}
'config' : {
    // default container is #myFeature
    'container' : $('#myFeature')
},

'init' : function(config) {
    // provide for custom configuration via init()
    if (config && typeof(config) == 'object') {
        $.extend(myFeature.config, config);
    }

    // create and/or cache some DOM elements
    // we'll want to use throughout the code
    myFeature.$container = myFeature.config.container;

    myFeature.$sections = myFeature.$container.
        // only select immediate children!
        find('ul.sections > li');

    myFeature.$section_nav = $('<p/>')
      .attr('id','section_nav')
      .prependTo(myFeature.$container);

    myFeature.$item_nav = $('<p/>')
      .attr('id','item_nav')
      .insertAfter(myFeature.$section_nav);

    myFeature.$content = $('<p/>')
      .attr('id','content')
      . insertAfter(myFeature.$item_nav);

  // build the section-level nav and
  // "click" the first item
  myFeature.buildSectionNav(myFeature.$sections);
  myFeature.$section_nav.find('li:first').click();

  // hide the plain HTML from sight
  myFeature.$container.find('ul.sections').hide();

  // make a note that the initialization
  // is complete; we don't strictly need this
  // for this iteration, but it can come in handy
  myFeature.initialized = true;
}
{% endcodeblock %}


<p>Next we’ll create the <code>myFeature.buildSectionNav()</code> method:</p>

{% codeblock lang:javascript %}
'buildSectionNav' : function($sections) {

    // iterate over the provided list of sections
    $sections.each(function() {

        // get the section
        var $section = $(this);

        // create a list item for the section navigation
        $('<li/>')
          // use the text of the first h2
          // in the section as the text for
          // the section navigation
          .text($section.find('h2:first').text())

          // add the list item to the section navigation
          .appendTo(myFeature.$section_nav)

          // use data() to store a reference
          // to the original section on the
          // newly-created list item
          .data('section', $section)

          // bind the click behavior
          // to the newly created list itme
          // so it will show the section
          .click(myFeature.showSection);
    });
}
{% endcodeblock %}

<p>Next we’ll create the <code>myFeature.buildItemNav()</code> method:</p>

{% codeblock lang:javascript %}
// iterate over the provided list of items
$items.each(function() {

    // get the item
    var $item = $(this);

    // create a list item element for the
    // item navigation
    $('<li>')

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

});
{% endcodeblock %}

<p>Finally, we’ll write the methods for showing sections and content items:</p>

{% codeblock lang:javascript %}
'showSection' : function() {
    // capture the list item that was clicked on
  var $li = $(this);

  // clear out the left nav and content area
  myFeature.$item_nav.empty();
  myFeature.$content.empty();

  // get the jQuery section object from the orginal HTML,
  // which we stored using data() during buildSectionNav
  var $section = $li.data('section');

  // mark the clicked list item as current
  // and remove the current marker from its siblings
  $li.addClass('current')
    .siblings().removeClass('current');

  // find all of the items related to the section
  var $items = $section.find('ul li');

  // build the item nav for the section
  myFeature.buildItemNav($items);

  // "click" on the first list item in the section's item nav
  myFeature.$item_nav.find('li:first').click();

},

'showContentItem' : function() {
  var $li = $(this);

  // mark the clicked list item as current
  // and revmoe the current marker from its siblings
  $li.addClass('current')
    .siblings().removeClass('current');

  // get the jQuery item object from the original HTML,
  // which we stored using data during buildContentNav
  var $item = $li.data('item');

  // use the item's HTML to populate the content area
  myFeature.$content.html($item.html());
}
{% endcodeblock %}


<p>All that’s left to do is to call the myFeature.init() method:</p>

{% codeblock lang:javascript %}
$(document).ready(myFeature.init);
{% endcodeblock %}

<h3>Step 4: Changing Requirements</h3>

<p>No project is complete without some last-minute change in the requirements, right? Here’s where the object literal approach really shines by making it quick and fairly painless to implement last-minute changes.  What if we need to get the content item excerpts via AJAX instead of from the HTML? Assuming the backend is set up to handle it, try this:</p>

{% codeblock lang:javascript %}
var myFeature = {

    'config' : {
        'container' : $('#myFeature'),

        // configurable function for getting
        // a URL for loading item content
        'getItemURL' : function($item) {
            return $item.find('a:first').attr('href');
        }

    },

    'init' : function(config) {
        // stays the same
    },

    'buildSectionNav' : function($sections) {
        // stays the same
    },

    'buildItemNav' : function($items) {
        // stays the same
    },

    'showSection' : function() {
        // stays the same
    },

    'showContentItem' : function() {

        var $li = $(this);

        $li.addClass('current').
            siblings().removeClass('current');

        var $item = $li.data('item');
        var url = myFeature.config.getItemURL($item);

        // myFeature.$content.html($item.html());
        myFeature.$content.load(url);

    }

};
{% endcodeblock %}


<p>Do you need more flexibility? There’s a lot more you can configure (and therefore override) if you really want to make this flexible. For example, you can use myFeature.config to specify how to find and process the title text for each item in the left nav.</p>

{% codeblock lang:javascript %}

var myFeature = {
    'config' : {
        'container' : $('#myFeature'),

        // specify the default selector
        // for finding the text to use
        // for each item in the item nav
        'itemNavSelector' : 'h3',

        // specify a default callback
        // for "processing" the jQuery object
        // returned by the itemNavText selector
        'itemNavProcessor' : function($selection) {
            return 'Preview of ' +
                $selection.eq(0).text();
        }
    },

    'init' : function(config) {
        // stays the same
    },

    'buildSectionNav' : function($sections) {
        // stays the same
    },

    'buildItemNav' : function($items) {

        $items.each(function() {
            var $item = $(this);

            // use the selector and processor
            // from the config
            // to get the text for each item nav
            var myText = myFeature.config.itemNavProcessor(
                $item.find(myFeature.config.itemNavSelector)
            );

            $('<li/>')
            // use the new variable
            // as the text for the nav item
              .text(myText)
              .appendTo(myFeature.$item_nav)
              .data('item', $item)
              .click(myFeature.showContentItem);
      });
  },

  'showSection' : function() {
    // stays the same
  },

  'showContentItem' : function() {
    // stays the same
  }

};
{% endcodeblock %}



<p>Once you’ve added defaults to the config object, you can override them when you call <code>myFeature.init()</code>:</p>

{% codeblock lang:javascript %}
$(document).ready(function() {
    myFeature.init({ 'itemNavSelector' : 'h2' });
});
{% endcodeblock %}

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
