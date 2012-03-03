---
layout: post
date: "2009-01-12"
title: jQuery validation and TinyMCE
comments: true
categories: howto, jquery
---

Just solved a problem where the <a href="http://docs.jquery.com/Plugins/Validation">jQuery validation plugin</a> wasn't playing so nicely with <a href="http://wiki.moxiecode.com/index.php/TinyMCE:Index">TinyMCE</a> -- the validation plugin was trying to validate the textarea before TinyMCE had a chance to copy the editor contents back to the textarea. I was about to yank TinyMCE out of the page but a little reading through the TinyMCE docs led me to try this:

{% codeblock lang:javascript %}
$('#mySubmitButton').click(function() {
    var content = tinyMCE.activeEditor.getContent(); // get the content
    $('#myTextarea').val(content); // put it in the textarea
});

$('#myForm').validate();
{% endcodeblock %}

And what do you know, it works. One note: it's important to bind the content replacement to the click event of the submit button, not to the actual form submission, or else the validation may try to run before the content gets copied back to the textarea.
