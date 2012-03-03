--- 
layout: post
date: "2009-12-03"
title: Demystifying custom events in jQuery
comments: true
categories: howto, javascript, jquery
---

<em>This article originally appeared in the May 2009 issue of <a href="http://jsmag.com">JSMag</a>.</em>

We’re all familiar with the basic events — click, mouseover, focus, blur, submit, etc. — that we can latch on to as a user interacts with the browser. Custom events open up a whole new world of event-driven programming. In this article, we’ll use jQuery’s custom events system to make a simple Twitter search application, but the general concepts should apply to any framework that supports custom events.

I confess: it took me a long time to decide to learn about custom events. The built-in events seemed to suit my needs just fine, and it was difficult to understand why I’d want to start adding my own.

Boy, was I missing out.

It turns out that custom events offer a whole new way of thinking about event-driven JavaScript. Instead of focusing on the element that triggers an action, custom events put the spotlight on the element being acted upon. This brings a bevy of benefits, including:

<ul>
<li>behaviors of the target element can easily be triggered by different elements using the same code; </li>
<li>behaviors can be triggered across multiple, similar, target elements at once; and </li>
<li>behaviors are more clearly associated with the target element in code, making code easier to read and maintain</li>
</ul>

Why should you care? An example is probably the best way to explain.

Suppose you have a lightbulb in a room in a house. The lightbulb is currently turned on, and it’s controlled by two three-way switches and a clapper, as shown here:

<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>


Triggering the clapper or either of the switches will change the state of the lightbulb. The switches and the clapper don’t care what state the lightbulb is in; they just want to change the state.

Without custom events, you might write some code like this:

<div class="CodeRay">
  <div class="code"><pre>$('.switch, .clapper').click(function() {
        var $light = $(this).parent().find('.lightbulb');
        if ($light.hasClass('on')) {
                $light.removeClass('on').addClass('off');
        } else {
                $light.removeClass('off').addClass('on');
        }
});</pre></div>
</div>


With custom events, your code might look more like this:

<div class="CodeRay">
  <div class="code"><pre>$('.lightbulb').bind('changeState', function(e) {
        var $light = $(this);
        if ($light.hasClass('on')) {
                $light.removeClass('on').addClass('off');
        } else {
                $light.removeClass('off').addClass('on');
        }
});
$('.switch, .clapper').click(function() {
        $(this).parent().find('.lightbulb').trigger('changeState');
});</pre></div>
</div>


This last bit of code is not that exciting, but something important has happened: we’ve moved the behavior of the lightbulb to the lightbulb, and away from the switches and the clapper.

Let’s make our example a little more interesting. We’ll add another room to our house, along with a master switch, as shown here:

<div class="CodeRay">
  <div class="code"><pre></pre></div>
</div>


If there are any lights on in the house, we want the master switch to turn all the lights off; otherwise, we want it to turn all lights on. To accomplish this, we’ll add two more custom events to the lightbulbs: turnOn and turnOff. We’ll make use of them in the changeState custom event, and use some logic to decide which one the master switch should trigger:

<div class="CodeRay">
  <div class="code"><pre>$('.lightbulb').
bind('changeState', function(e) {
        var $light = $(this);
        if ($light.hasClass('on')) {
                $light.trigger('turnOff');
        } else {
                $light.trigger('turnOn');
        }
}).
bind('turnOn', function(e) {
        $(this).removeClass('off').addClass('on');
}).
bind('turnOff', function(e) {
        $(this).removeClass('off').addClass('on');
});

$('.switch, .clapper').click(function() {
        $(this).parent().find('.lightbulb').trigger('changeState');
});

$('#master_switch').click(function() {
        if ($('.lightbulb.on').length) {
                $('.lightbulb').trigger('turnOff');
        } else {
                $('.lightbulb').trigger('turnOn');
        }
});</pre></div>
</div>


A bit more interesting, huh? Note how the rules about what the master switch does belongs to the master switch; the rules about how a lightbulb turns on and off belong to the lightbulb. It’s also worth noting that the master switch is able to affect all of the lightbulbs without having to iterate over them — it just triggers an event on all elements that have the class “lightbulb”. This isn’t critical to the example, but if we were to perform more elaborate manipulations of each lightbulb than adding and removing a class, this would be an important benefit of custom events, as you’ll see in the rest of the article.

(If you’re accustomed to object-oriented programming, you may find it useful to think of custom events as methods of objects. Loosely speaking, the object to which the method belongs is created via the jQuery selector. Binding the changeState custom event to all $('.light') elements is akin to having a class called Light with a method of changeState(), and then instantiating new Light objects for each element with a classname of light.)

<h2>jQuery Event Primer</h2>
Before we dive in, a couple of things we need to recap. In the world of custom events, there are two important jQuery methods: .bind() and .trigger(). I encourage you to read the jQuery docs for details, but basically:

<ul>
<li>The .bind() method takes an event type and an event handling function as arguments. Optionally, it can also receive data, which will be available to the event handling function in the data property of the event object. The event handling function always receives the event object as its first argument.</li>
<li>The .trigger() method takes an event type as its argument. Optionally, it can also take an array of values. The first item in the array will be the second argument passed to the event handling function (after the event object).</li>
</ul>

Confused? I don’t blame you. Read on and I’ll try to clear some things up.

<h2>Our Mission</h2>
To demonstrate the power of custom events, we’re going to create a simple tool for searching Twitter. The tool will offer several ways for a user to add search terms to the display: by entering a search term in a text box, by entering multiple search terms in the URL, and by querying Twitter for trending terms.

The results for each term will be shown in a results container; these containers will be able to be expanded, collapsed, refreshed, and removed, either individually or all at once.

When we’re done, it will look like this:



<h2>The Setup</h2>
We’ll start with some basic HTML:

<div class="CodeRay">
  <div class="code"><pre>Twitter Search</pre></div>
</div>
<form>
	<input class="input_text" type="text" />
	<input class="input_submit" value="Add Search Term" type="submit" />
</form>

<div>
	<div class="template results">
		<h2>Search Results for
		<span class="search_term"></span>
</h2>
	</div>
</div>


This gives us a container (#twitter) for our widget, a template for our results containers (hidden via CSS), and a simple form where users can input a search term. (For the sake of simplicity, we’re going to assume that our application is JavaScript-only and that our users will always have CSS.)

First, the setup. We’ll build an $actions object that we’ll use later to create the buttons in each results container, and we’ll also create a global search_terms object so we can store a list of search terms that are being displayed on the page.
Next, we’ll do our custom event binding. There are two types of objects we’ll want to act on: the results containers, and the Twitter container.

<h3>The Results Containers</h3>
The results containers are the heart of the application. We’ll create a setupResults() plugin that will prepare each results container once it’s added to the Twitter container. Among other things, it will bind the custom events for each container and add the action buttons at the top right of each container. Each results container will have the following custom events:

<ul>
<li>The refresh event will mark the container as being in the “refreshing” state, and fire the $.getJSON() request to fetch the data for the search term.</li>

<li>The populate event will receive the returned JSON data and use it to populate the container.</li>

<li>The remove event will remove the container from the page after the user verifies the request to do so. Verification can be bypassed by passing true as the second argument to the event handler. The remove event also removes the term associated with the results container from the global search_terms object.</li>

<li>The collapse event will add a class of collapsed to the container, which will hide the results via CSS. It will also turn the container’s “Collapse” button into an “Expand” button.</li>

<li>The expand event will remove the collapsed class from the container. It will also turn the container’s “Expand” button into a “Collapse” button.</li>
</ul>

The plugin is also responsible for adding the action buttons to the container by cloning the $actions object that was created earlier. It binds a click event to each action’s list item, and uses the list item’s class to determine which custom event will be triggered on the corresponding results container.

<div class="CodeRay">
  <div class="code"><pre>// we'll use this every time we add a new results panel,
// so let's build it once and cache it in $actions
var $actions = $('</pre></div>
</div>
<ul class="actions"></ul>');
$('<li class="refresh">Refresh</li>').appendTo($actions);
$('<li class="remove">Remove</li>').appendTo($actions);
$('<li class="collapse">Collapse</li>').appendTo($actions);

// this is where we'll keep track of which search terms
// are shown on the page already
var search_terms = {};


Here's the setupResults plugin:

<div class="CodeRay">
  <div class="code"><pre>$.fn.setupResults = function(settings) {
        return $(this).each(function() {

                var $results = $(this);
                var $actions = settings.actions;
                var term = settings.term;

                // change the &quot;Search results for&quot; text
                $results.find('span.search_term').text(term);

                // bind custom events for results box
                $results.

                // the &quot;refresh&quot; event fetches
                // the latest content for the term
                bind('refresh', function(e) {
                        // indicate that the results are refreshing
                        var $this = $(this).addClass('refreshing');

                        $this.find('p.tweet').remove();
                        $results.append('Loading ...');

                        // get the twitter data using jsonp
                        $.getJSON(
                                'http://search.twitter.com/search.json?q=' +                                         escape(term) + '&amp;rpp=5&amp;callback=?',
                                function(json) {
                                        $this.trigger('populate', [ json ]);
                                }
                        );
                }).

                // the &quot;populate&quot; event takes results
                // in json format
                // and populates the results container
                bind('populate', function(e, json) {
                        var results = json.results;
                        var $this = $(this);

                        $this.find('p.loading').remove();

                        $.each(results, function(i,result) {
                                var tweet = '' +
                                        '' +
                                        result.from_user +
                                        ': ' +
                                        result.text +
                                        ' ' +
                                        result.created_at +
                                        '' +
                                '';
                                $this.append(tweet);
                                        });

                                        // indicate that the results
                                        // are done refreshing
                                        $this.removeClass('refreshing');
                                }).
                                                // the remove event removes
                                                // the results from the page
                                                // after the user confirms the action
                                                bind('remove', function(e, force) {
                                                        // allow forced removal without confirmation
                                                        if (
                                                                !force &amp;&amp;
                                                                !confirm('Remove panel for term ' + term + '?')
                                                        ) {
                                                                return;
                                                        }
                                                        $(this).remove();

                                                        // indicate that we no longer
                                                        // have a panel for the term
                                                        search_terms[term] = 0;
                                                }).

                                                // the collapse event collapses the results so only the
                                                // header of the results section is showing
                                                bind('collapse', function(e) {
                                                        $(this).find('li.collapse').removeClass('collapse')
                                                                .addClass('expand').text('Expand');

                                                        $(this).addClass('collapsed');
                                                }).

                                                // the expand event
                                                bind('expand', function(e) {
                                                        $(this).find('li.expand').removeClass('expand')
                                                                .addClass('collapse').text('Collapse');

                                                        $(this).removeClass('collapsed');
                                                });

                                                if ($actions &amp;&amp; $actions.length) {
                                                        // add a clone of $actions to the results panel
                                                        var $a = $actions.clone().prependTo($results);

                                                        // use the class of each action to figure out
                                                        // which event it will trigger on the results panel
                                                        $a.find('li').click(function() {
                                                                // pass the li that was clicked to the function
                                                                // so it can be manipulated if needed
                                                                $results.trigger(
                                                                        $(this).attr('class'), [ $(this) ]
                                                                );
                                                        });
                                                }
                                        });
                                };</pre></div>
</div>


<h3>The Twitter Container</h3>
The Twitter container itself will have just two custom events:
<ul>
<li>The getResults event will receive a search term. It will check the global search_terms object to determine whether there’s already a results container for the term; if not, it will add a results container using the results template (div.template), set up the results container using the setupResults() plugin discussed above, and then trigger the refresh event on the results container in order to actually load the results. Finally, it will store the search term in the global search_terms object, so the application knows not to re-fetch the term.</li>
<li>The getTrends event will query Twitter for the top 10 trending terms. It will iterate over them and trigger the widget’s getResults event for each of them, thereby adding a results container for each term. Here you can see how we go about passing data to a triggered event.</li>
</ul>

The Twitter container bindings are shown here:

<div class="CodeRay">
  <div class="code"><pre>$('#twitter').
bind('getResults', function(e, term) {
        // make sure we don't have a box for this term already
        if (!search_terms[term]) {
                var $this = $(this);
                var $template = $this.find('div.template');

                // make a copy of the template div
                // and insert it as the first results box
                $results = $template.clone().
                        removeClass('template').
                        insertBefore($this.find('div:first')).
                        setupResults({
                                'term' : term,
                                'actions' : $actions
                        });

                // load the content using the &quot;refresh&quot;
                // custom event that we bound to the results container
                $results.trigger('refresh');
                search_terms[term] = 1;
        }
}).

bind('getTrends', function(e) {
        var $this = $(this);
        $.getJSON('http://search.twitter.com/trends.json?callback=?',                 function(json) {
                        var trends = json.trends;
                        $.each(trends, function(i, trend) {
                                $this.trigger('getResults', [ trend.name ]);
                        });
                });
});</pre></div>
</div>


So far, we’ve written a lot of code that does approximately nothing, but that’s OK. By specifying all the behaviors that we want our core objects to have, we’ve created a solid framework for rapidly building out the interface.

Let’s start by hooking up our text input and the “Load Trending Terms” button. For the text input, we’ll make the form submission stop in its tracks using e.preventDefault(), then capture the term that was entered in the input and pass it to the Twitter widget’s getResults event. (Again, you can see how we go about passing data to a triggered event.) Clicking the “Load Trending Terms” will simply trigger the Twitter widget’s getTrends event:

<div class="CodeRay">
  <div class="code"><pre>$(document).ready(function() {

$('form').submit(function(e) {
        e.preventDefault();
        var term = $('#search_term').val();
        $('#twitter').trigger('getResults', [ term ]);
});

$('#get_trends').click(function() {
        $('#twitter').trigger('getTrends');
});

});</pre></div>
</div>


Just entering a search term into a text box is boring, of course — the following code shows how we can capture search terms from the URL’s hash (e.g., <a href="http://foo.com/index.html#foo">http://foo.com/index.html#foo</a>,bar):

<div class="CodeRay">
  <div class="code"><pre>$(document).ready(function() {

// pass search terms via URL hash
if (document.location.hash) {
        var terms = document.location.hash.split(',').reverse();
        $.each(terms, function(i,term) {
                $('#twitter').trigger('getResults', [ term ]);
        });
}

});</pre></div>
</div>


By adding a few buttons with the appropriate IDs, we can make it possible to remove, collapse, expand, and refresh all results containers at once, as shown below. For the remove button, note how we’re passing a value of true to the event handler as its second argument, telling the event handler that we don’t want to verify the removal of individual containers.

<div class="CodeRay">
  <div class="code"><pre>$(document).ready(function() {

$('#refresh').click(function(e) {
        $('#twitter div.results').trigger('refresh');
});

$('#expand').click(function(e) {
        $('#twitter div.results').trigger('expand');
});

$('#collapse').click(function(e) {
        $('#twitter div.results').trigger('collapse');
});

$('#remove').click(function(e) {
        if (confirm('Remove all results?')) {
                $('#twitter div.results').
                        trigger('remove', [ true ]);
        }
});
});</pre></div>
</div>


It should be noted that you don’t have to choose one or all results containers — really, you can choose any results containers you want to affect. For example, if for some (strange) reason you wanted to refresh only the first and last results containers, you could do:

<div class="CodeRay">
  <div class="code"><pre>$('div.results:first, div.results:last').
        trigger('refresh');</pre></div>
</div>


You can see the entire application, including the full HTML and CSS, at <a href="http://www.rebeccamurphey.com/jsmag/custom-events/">http://www.rebeccamurphey.com/jsmag/custom-events/</a>.

<h2>Conclusion</h2>
Custom events offer a new way of thinking about your code: they put the emphasis on the target of a behavior, not on the element that triggers it. If you take the time at the outset to spell out the pieces of your application, as well as the behaviors those pieces need to exhibit, custom events can provide a powerful way for you to “talk” to those pieces, either one at a time or en masse. Once the behaviors of a piece have been described, it becomes trivial to trigger those behaviors from anywhere, allowing for rapid creation of and experimentation with interface options. Finally, custom events can enhance code readability and maintainability, by making clear the relationship between an element and its behaviors.

<h2>Learn More</h2>
<ul>
	<li><a href="http://docs.jquery.com/Events/jQuery.Event">The jQuery Event object</a></li>
	<li><a href="http://docs.jquery.com/Events/bind#typedatafn">jQuery’s bind() method</a></li>
	<li><a href="http://docs.jquery.com/Events/trigger#eventdata">jQuery’s trigger() method</a></li>
	<li><a href="http://remysharp.com/2007/10/08/what-is-jsonp/">jQuery and JSON-P</a></li>
	<li><a href="http://en.wikipedia.org/wiki/The_Clapper">Need I say more?</a></li>
</ul>