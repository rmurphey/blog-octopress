---
layout: post
date: "2008-01-06"
title: Unobtrusive, cross-browser method to add icons to links
comments: true
categories: front-end development, howto, jquery
---

There are <a href="http://www.askthecssguy.com/2006/12/showing_hyperlink_cues_with_cs_1.html">lots</a> of <a href="http://www.creativewebspecialist.co.uk/CSS_attributes/">examples</a> of using CSS to add filetype icons to links, but they all rely on advanced CSS selectors, which Internet Explorer 6 doesn't support.

If you're looking for a cross-browser method of adding filetype icons to links, you have a couple of choices: you can add a class name to all of your links and target that class name with CSS that IE6 can understand; or you can let Javascript add the links dynamically based on the extension it finds at the end of each URL. The class name method is fine if you don't mind adding the class to all of your links in your HTML, but the Javascript method will figure out which links get which icon automatically.

<h3>The Javascript/jQuery method</h3>
This uses my favorite Javascript library, <a href="http://jquery.com">jQuery</a>; you'll need to include the library in your document, and then either put the icon-adding code in another file you include, or put it inline with your HTML. It's good practice to put all your jQuery code inside <code>$.ready()</code>, <a href="http://docs.jquery.com/Events/ready#fn">to be executed when the document is ready to be manipulated</a>, but I've left that part out here for brevity.

<strong>Round 1:</strong> Because <a href="http://docs.jquery.com/Selectors">jQuery supports CSS1-3</a>, you can mimic the CSS rule that wouldn't work in IE6:

{% codeblock lang:javascript %}
$('a[href$=&quot;.doc&quot;]').
  css({
    // use paddingLeft instead of padding-left;
    // jQuery (and Javascript) use camelCase
    // for CSS attributes instead of hyphenation
    paddingLeft: '18px',
    background: 'transparent url(&quot;word-doc.gif&quot;) no-repeat center left'
  });
{% endcodeblock %}


<strong>Round 2:</strong> You <em>could</em> do one of these snippets for each file type, but with jQuery, you can take advantage of the <a href="http://docs.jquery.com/Utilities/jQuery.each#objectcallback">$.each() utility method</a> to do a loop, eliminating redundant code:

{% codeblock lang:javascript %}
// first, create an object
// that contains information
// about how file extensions
// correspond to images

var fileTypes = {
  // extension: 'image file'
  doc: 'doc.gif',
  xls: 'xls.gif',
  pdf: 'pdf.gif'
};

// then, loop over the object
// using jQuery's $.each()
$.each(fileTypes, function(extension,image) {
  $('a[href$=&quot;' + extension + '&quot;]').
    css({
      paddingLeft: '18px',
      background: 'transparent url(&quot;' + image + '&quot;) no-repeat center left'
    });
});
{% endcodeblock %}


<strong>Round 3:</strong> One problem with this: while jQuery does support these attribute selectors, in my experience they require some pretty heavy lifting to do the pattern matching, which can slow things down significantly. Since the method above would require multiple selections (one for each extension/image combination), it makes sense to try a different way that will require fewer selections and less pattern matching:

{% codeblock lang:javascript %}
var fileTypes = {
  doc: 'doc.gif',
  xls: 'xls.gif',
  pdf: 'pdf.gif'
};

// this is like $.each() above, except
// it iterates over the matched elements
$('a').each(function() {

  // get a jQuery object for each anchor found
  var $a = $(this);

  // get the href attribute
  var href = $a.attr('href');

  // get the extension from the href
  var hrefArray = href.split('.');
  var extension = hrefArray[hrefArray.length - 1];

  var image = fileTypes[extension];

  if (image) {
    $a.css({
      paddingLeft: '18px',
      background: 'transparent url(&quot;' + image + '&quot;) no-repeat center left'
    });
  }

});
{% endcodeblock %}


And in fact, in limited testing using Firebug, this second version is faster than the first if you have more than two filetypes (and thus more than two selections).

<h3>Taking it further</h3>
You can also add a different icon to external links, and only add filetype icons to internal links:

{% codeblock lang:javascript %}
var fileTypes = {
  doc: 'doc.gif',
  xls: 'xls.gif',
  pdf: 'pdf.gif'
};

$('a').each(function() {

  var $a = $(this);
  var href = $a.attr('href');

  if (
    (href.match(/^http/)) &amp;&amp;
    (! href.match(document.domain))
  ) {

    // use a special image for external links
    var image = 'external.gif';

  } else {
    // get the extension from the href
    var hrefArray = href.split('.');
    var extension = hrefArray[hrefArray.length - 1];

    var image = fileTypes[extension];
  }

  if (image) {
    $a.css({
      paddingLeft: '18px',
      background: 'transparent url(&quot;' + image + '&quot;) no-repeat center left'
    });
  }

});
{% endcodeblock %}


Or, only add icons to certain links, like a list of files in an unordered list, by changing your selector from <code>$('a')</code> to <code>$('ul#fileList a')</code>.

<h3>Resources</h3>
<ul>
<li>There are nuances to the CSS you'll want to apply to your links; the above is just an example of something that may or may not work for you. <a href="http://www.askthecssguy.com/2006/12/showing_hyperlink_cues_with_cs_1.html">Read more here</a> about how to style the links so your background images appear as you want them to.</li>
<li>If you're using transparent PNGs for your background images, <a href="http://www.twinhelix.com/css/iepngfix/">iepngfix</a> will save the day.</li>
<li>
<a href="http://www.famfamfam.com/lab/icons/silk/">famfamfam</a> has some great icons available for free under the Creative Commons Attribution 2.5 License.</li>

</ul>

