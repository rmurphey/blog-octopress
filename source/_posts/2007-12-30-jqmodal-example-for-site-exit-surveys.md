--- 
layout: post
date: "2007-12-30"
title: jqModal example for site exit surveys
comments: true
categories: ajax login, howto, javascript, jquery, popup, survey
---

A couple of months ago, a client wanted us to add a survey to one of their microsites to gauge user response. The idea was that if the user clicked a link to a page outside the microsite, then they'd see an in-browser popup window asking them to take a survey. Clicking yes would take them to the survey; clicking no would take them to their intended destination.

[Let me pause for a moment to say that I think site exit surveys suck. They involve violating the user's expectations; when a user clicks on a link, the user expects to go to the link destination. My opinion hasn't kept clients from asking for them, though. That said, the code below has less nefarious applications as well: a friend is using something very similar to display a login/registration popup (hooked up to the back end with AJAX) when the user clicks on a link requiring authentication.]

When I solved this a couple of months ago, I used <a href="http://jquery.com/demo/grey/">Greybox</a> to load a separate HTML page into the page to display the survey; since then, I've learned about the much-easier-to-use <a href="http://dev.iceburg.net/jquery/jqModal/">jqModal</a>. I had to make several changes to Greybox to get it to do what I wanted, but jqModal works really easily out of the box (and is a much more appropriate tool).

First, I added a div to my HTML with the contents of the popup (though jqModal will also let you get the popup contents with an AJAX request). I used the default jqModal CSS to style the popup window, but be sure to see the example page to fully grasp all you can do with the popup window.

Here's the HTML for the popup:

<div class="CodeRay">
  <div class="code"><pre>This is the survey
  Won't you please take our survey?</pre></div>
</div>


With that set up, I needed to initialize the popup window, and then set it up so clicks on exit links would trigger the popup window. jqModal lets you pass a CSS selector to the <code>trigger</code> option so the popup will appear when you click on an element that matches the selector. I wanted to capture the href of the link that was clicked, so I needed to do it a little differently. Here's what I ended up with:

<div class="CodeRay">
  <div class="code"><pre>$().ready(function() {

  // location of the actual survey
  var surveyUrl = 'survey.html';

  // initialize the popup
  $('#popup').
    jqm({
      overlay: 88, // opacity of the overlay
      modal: true, // prevent clicks outside popup
      trigger: false // trigger is handled separately
    }).

    // close the popup when user clicks the close button
    jqmAddClose('#popup input.close');

  // send user to the survey if they click yes
  $('#popup input.yes').
    click(function() { location.href = surveyUrl; });

  // trigger the survey if users clicks an exit link
  $('a.exitLink').click(function(e) {

    // don't go to the link destination just yet
    e.preventDefault();

    // get the link destination
    var href = $(this).attr('href');

    // show the popup
    $('#popup').
      jqmShow().

      // attach the link destination
      // to the &quot;no thanks&quot; button
      find('input.no').
        click(function() { location.href = href; });
  });
});</pre></div>
</div>


<a href="http://www.rebeccamurphey.com/jquery/survey/survey.html">Here's the whole thing in action</a>. I put the <code>exitLink</code> class on the link by hand, but you could also do it by using jQuery to find links whose hrefs match a certain pattern and add the class to them.

<div class="CodeRay">
  <div class="code"><pre>// find all links whose hrefs begin with http://
// (from http://simonwillison.net/2007/Aug/15/jquery/)
$('a[@href^=&quot;http://&quot;]').addClass('exitLink');</pre></div>
</div>