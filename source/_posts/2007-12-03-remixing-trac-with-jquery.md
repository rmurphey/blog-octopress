---
layout: post
date: "2007-12-03"
title: Remixing trac with jQuery
comments: true
categories:
  - front-end development
  - javascript
  - jquery
  - trac
---

I love <a href="http://trac.edgewall.org/">trac</a>, and I've been doing lots of work lately to customize it so our clients can use it to send us requests. With the configurable workflow option offered in <a href="http://trac.edgewall.org/milestone/0.11">version 0.11</a>, it became the perfect way for us to manage and track client requests. By connecting it to the subversion repository we use for version control of client files, we've virtually eliminated the need for back-and-forth emails -- everything is in one place.

Since trac is intended more for managing a development project than for managing an ongoing client relationship, I've had to make some changes to the out-of-the-box version. I don't know Python, and I was having a hard time getting a developer to dive in at the level I needed on a non-billable project. Lucky for me, version 0.11 also includes jQuery (version 1.1.3.1). After a couple of chats with a developer and a few "duh" moments, it became clear that I could do most of what I needed without messing around under the hood.

The story so far:

<ul>
<li>Our clients use trac for internal approval of a request before it gets sent to us -- a content provider can enter a request, which then gets reviewed by a manager. We needed a way to assign this manager to the ticket, without the manager becoming the "owner" of the ticket. Initially, we had content providers entering their manager's email address into the Cc: field, but that didn't go over well. Now, jQuery reuses the existing Assign to menu to build a manager dropdown, with the usernames of our employees removed. When the ticket is submitted, the manager is added to the Cc: field automatically.

{% codeblock lang:javascript %}
// build the new dropdown
var custom_cc_select = '';
$('#custom-form').append(custom_cc_select);
$('#field-owner option').each(function() {
  var name = $(this).html();
  if ($.inArray(name,excludedManagers) == -1) {
    $('#custom-cc').append('' + name + '');
  }
});

// add the selected manager to the cc field on ticket submit
$('div.ticket form').submit(function() {
  var oldCc = $('#field-cc').val();
  var manager = $('#custom-cc option:selected').html();
  if (oldCc != '') { $('#field-cc').val(oldCC + ', ' + manager); }
  else { $('#field-cc').val(manager); }
});
{% endcodeblock %}

</li>

<li>The standard trac ticket fields were exactly what we needed, but the labels for them were a little confusing to client users. Plus, the form was ugly. A few lines of jQuery created a new section in the form, moved the fields to that section without their table-based layout, reordered the fields and and relabeled them. For example:

{% codeblock lang:javascript %}
$('#field-component'). // get the component dropdown
remove(). // remove it from the DOM but maintain access to it
appendTo('#custom-form'). // put it at the end of the #custom-form div
before('Request Type'); // put a new label before it
{% endcodeblock %}

The new form makes more sense to the client users, reduces errors and omissions, and looks better too.</li>

<li>For certain client requests, a more complex form is required. Trac offers custom fields, but that solution seemed too limiting. With a few more lines of jQuery, I set up a system that watches the Component dropdown. When certain components are chosen, the standard submission form is hidden, and a new form is put in its place using jQuery's load() method. The replacement content can include HTML, standard form elements and javascript; when the form is submitted, javascript assembles the field values into a well-formatted description.</li>
</ul>
