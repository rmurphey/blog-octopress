---
layout: post
title: "Lessons From a Rewrite"
date: 2011-07-06 10:50
comments: true
categories: toura, javascript, dojo, large apps
---

MVC and friends have been around for decades, but it’s only in the last couple
of years that broad swaths of developers have started applying those patterns
to JavaScript. As that awareness spreads, developers eager to use their
newfound insight are presented with a target-rich environment, and the
temptation to rewrite can be strong.

> There’s a subtle reason that programmers always want to throw away the code
and start over. The reason is that they think the old code is a mess. … The reason
that they think the old code is a mess is because of a cardinal, fundamental
law of programming: It’s harder to read code than to write it. - [ Joel Spolsky
](http://www.joelonsoftware.com/articles/fog0000000069.html)

When I started working with [ Toura Mobile ](http://toura.com/) late last year, they already had
a product: a web-based CMS to create the structure of a mobile application and
populate it with content, and a PhoneGap-based application to consume the
output of the CMS inside a native application. Customers were paying, but the
development team was finding that delivering new features was a struggle, and
bug fixes seemed just as likely to break something else as not. They contacted
me to see whether they should consider a rewrite.

With due deference to Spolsky, I don’t think it was a lack of readability
driving their inclination to rewrite. In fact, the code wasn’t all that
difficult to read or follow. The problem was that the PhoneGap side of things
had been written to solve the problems of a single-purpose, one-off
application, and it was becoming clear that it needed to be a flexible,
extensible delivery system for all of the content combinations clients could
dream up. It wasn’t an app — it was an app that made there be an app.

> Where a new system concept or new technology is used, one has to build a system
to throw away, for even the best planning is not so omniscient as to get it
right the first time. Hence plan to throw one away; you will, anyhow. - Fred
Brooks, [ The Mythical Man Month ](http://en.wikipedia.org/wiki/The_Mythical_Man-Month)

By the time I’d reviewed the code and started writing up my findings, the
decision had already been made: Toura was going to throw one away and start
from scratch. For four grueling and exciting months, I helped them figure out
how to do it better the second time around. In the end, I like to think we’ve
come up with a solid architecture that’s going to adapt well to clients’
ever-changing needs. Here, then, are some of the lessons we learned along the
way.

## Understand what you’re rewriting

I had spent only a few days with the codebase when we decided that we were
going to rewrite it. In some ways, this was good — I was a fresh set of eyes,
someone who could think about the system in a new way — but in other ways, it
was a major hindrance. We spent a lot of time at the beginning getting me up to
speed on what, exactly, we were making; things that went without saying for
existing team members did not, in fact, go without saying for me.

This constant need for explanation and clarification was frustrating at times,
both for me and for the existing team, but it forced us to state the problem in
plain terms. The value of this was incredible — as a team, we were far less
likely to accept assumptions from the original implementation, even assumptions
that seemed obvious.

One of the key features of Toura applications is the ability to update them
“over the air” — it’s not necessary to put a new version in an app store in
order to update an app’s content or even its structure. In the original app,
this was accomplished via generated SQL diffs of the data. If the app was at
version 3, and the data in the CMS was at version 10, then the app would
request a patch file to upgrade version 3 to version 10. The CMS had to
generate a diff for all possible combinations: version 3 to version 10, version
4 to version 10, etc. The diff consisted of queries to run against an SQLite
database on the device. Opportunities for failures or errors were rampant,
a situation exacerbated by the async nature of the SQLite interface.

In the new app, we replicated the feature with vastly less complexity
— whenever there is an update, we just make the full data available at an
app-specific URL as a JSON file, using the same format that we use to provide
the initial data for the app on the device. The new data is stored on the
device, but it’s also retained in memory while the application is running via
Dojo’s Item File Read Store, which allows us to query it synchronously. The
need for version-by-version diffs has been eliminated.

Restating the problem led to a simpler, more elegant solution that greatly
reduced the opportunities for errors and failure. As an added benefit, using
JSON has allowed us to meet needs that we never anticipated — the flexibility
it provides has become a valuable tool in our toolbox.

## Identify pain points

If the point of a rewrite is to make development easier, then an important step
is to figure out what, exactly, is making development hard. Again, this was
a time to question assumptions — as it turned out, there were things that had
come to be accepted burdens that were actually relatively easy to address.

One of the biggest examples of this was the time required to develop and test
anything that might behave differently on one operating system versus another.
For example, the Android OS has limited support for the audio and video tags,
so a native workaround is required to play media on Android that is not
required on iOS.

In the original code, this device-specific branching was handled in a way that
undoubtedly made sense at the beginning but grew unwieldy over time. Developers
would create Mustache templates, wrapping the template tags in `/* */` so the
templates were actually executable, and then compile those templates into plain
JavaScript files for production. Here are a few lines from one of those
templates:

{% codeblock lang:javascript %}
/* {{^android}} */
var mediaPath = "www/media/" + toura.pages.currentId + "/";
/* {{/android}} */
/* {{#android}} */
var mediaPath = [Toura.getTouraPath(), toura.pages.currentId].join("/");
/* {{/android}} */
var imagesList = [], dimensionsList = [], namesList = [], thumbsList = [];
var pos = -1, count = 0;
/* {{#android}} */
var pos = 0, count = 0;
/* {{/android}} */
{% endcodeblock %}

These templates were impossible to check with a code quality tool like JSHint,
because it was standard to declare the same variable multiple times. Multiple
declarations of the same variable meant that the order of those declarations
was important, which made the templates tremendously fragile. The theoretical
payoff was smaller code in production, but the cost of that byte shaving was
high, and the benefit somewhat questionable — after all, we’d be delivering the
code directly from the device, not over HTTP.

In the rewrite, we used a simple configuration object to specify information
about the environment, and then we look at the values in that configuration
object to determine how the app should behave. The configuration object is
created as part of building a production-ready app, but in development we can
alter configuration settings at will. Simple `if` statements replaced fragile
template tags.

Since Dojo allows specifying code blocks for exclusion based on the settings
you provide to the build process, we could mark code for exclusion if we really
didn’t want it in production.

By using a configuration object instead of template tags for branching, we
eliminated a major pain point in day-to-day development. While nothing matches
the proving ground of the device itself, it’s now trivial to effectively
simulate different device experiences from the comfort of the browser. We do
the majority of our development there, with a high degree of confidence that
things will work mostly as expected once we reach the device. If you’ve ever
waited for an app to build and install to a device, then you know how much
faster it is to just press Command-R in your browser instead.

## Have a communication manifesto

Deciding that you’re going to embrace an MVC-ish approach to an application is
a big step, but only a first step — there are a million more decisions you’re
going to need to make, big and small. One of the widest-reaching decisions to
make is how you’ll communicate among the various pieces of the application.
There are all sorts of levels of communication, from application-wide state
management — what page am I on? — to communication between UI components — when
a user enters a search term, how do I get and display the results?

From the outset, I had a fairly clear idea of how this should work based on
past experiences, but at first I took for granted that the other developers
would see things the same way I did, and I wasn’t necessarily consistent
myself. For a while we had several different patterns of communication,
depending on who had written the code and when. Every time you went to use
a component, it was pretty much a surprise which pattern it would use.

After one too many episodes of frustration, I realized that part of my job was
going to be to lay down the law about this — it wasn’t that my way was more
right than others, but rather that we needed to choose a way, or else reuse and
maintenance was going to become a nightmare. Here’s what I came up with:

- `myComponent.set(key, value)` to change state (with the help of setter
  methods from Dojo’s [dijit.\_Widget mixin](http://dojotoolkit.org/reference-guide/dijit/_Widget.html))
- `myComponent.on&lt;Event&gt;(componentEventData)` to announce state changes
  and user interaction; Dojo lets us
  [connect](http://dojotoolkit.org/reference-guide/dojo/connect.html) to the
  execution of arbitrary methods, so other pieces could listen for these
  methods to be executed.
- `dojo.publish(topic, [ data ])` to announce occurrences of app-wide interest,
  such as when the window is resized
- `myComponent.subscribe(topic)` to allow individual components react to
  published topics

Once we spelled out the patterns, the immediate benefit
wasn’t maintainability or reuse; rather, we found that we didn’t have to make
these decisions on a component-by-component basis anymore, and we could focus
on the questions that were actually unique to a component. With conventions
we could rely on, we were constantly discovering new ways to abstract and DRY
our code, and the consistency across components meant it was easier to work
with code someone else had written.

## Sanify asynchronicity

One of the biggest challenges of JavaScript development — well, besides working
with the DOM — is managing the asynchronicity of it all. In the old system,
this was dealt with in various ways: sometimes a method would take a success
callback and a failure callback; other times a function would return an object
and check one of its properties on an interval.

{% codeblock lang:javascript %}
images = toura.sqlite.getMedias(id, "image");

var onGetComplete = setInterval(function () {
  if (images.incomplete)
    return;

  clearInterval(onGetComplete);
  showImagesHelper(images.objs, choice)
},10);
{% endcodeblock %}

The problem here, of course, is that if `images.incomplete` never gets set to
false — that is, if the `getMedias` method fails — then the interval will never
get cleared. Dojo and now jQuery (since version 1.5) offer a facility for
handling this situation in an elegant and powerful way. In the new version of
the app, the above functionality looks something like this:

{% codeblock lang:javascript %}
toura.app.Data.get(id, ‘image’).then(showImages, showImagesFail);
{% endcodeblock %}

The `get` method of `toura.app.Data` returns an [immutable promise](http://www.sitepen.com/blog/2010/05/03/robust-promises-with-dojo-deferred-1-5/)
— the promise’s then method makes the resulting value of the asynchronous get
method available to `showImages`, but does not allow `showImages` to alter the
value. The promise returned by the get method can also be stored in a variable,
so that additional callbacks can be attached to it.

Using promises vastly simplifies asynchronous code, which can be one of the
biggest sources of complexity in a non-trivial application. By using promises,
we got code that was easier to follow, components that were thoroughly
decoupled, and new flexibility in how we responded to the outcome of an
asynchronous operation.

## Naming things is hard

Throughout the course of the rewrite we were constantly confronted with one of
those pressing questions developers wrestle with: what should I name this
variable/module/method/thing? Sometimes I would find myself feeling slightly
absurd about the amount of time we’d spend naming a thing, but just recently
I was reminded how much power those names have over our thinking.

Every application generated by the Toura CMS consists of a set of “nodes,”
organized into a hierarchy. With the exception of pages that are standard
across all apps, such as the search page, the base content type for a page
inside APP is always a node — or rather, it was, until the other day. I was
working on a new feature and struggling to figure out how I’d display a piece
of content that was unique to the app but wasn’t really associated with a node
at all. I pored over our existing code, seeing the word node on what felt like
every other line. As an experiment, I changed that word node to baseObj in
a few high-level files, and suddenly a whole world of solutions opened up to me
— the name of a thing had limiting my thinking.

The lesson here, for me, is that the time we spent (and spend) figuring out
what to name a thing is not lost time; perhaps even more importantly, the goal
should be to give a thing the most generic name that still conveys what the
thing’s job — in the context in which you’ll use the thing — actually is.

## Never write large apps

I touched on this earlier, but if there is one lesson I take from every large
app I’ve worked on, it is this:

> The secret to building large apps is never build large apps. Break up your
applications into small pieces. Then, assemble those testable, bite-sized
pieces into your big application. - Justin Meyer

The more tied components are to each other, the less reusable they will be, and
the more difficult it becomes to make changes to one without accidentally
affecting another. Much like we had a manifesto of sorts for communication
among components, we strived for a clear delineation of responsibilities among
our components. Each one should do one thing and do it well.

For example, simply rendering a page involves several small, single-purpose
components:

{% codeblock lang:javascript %}
function nodeRoute(route, nodeId, pageState) {
  pageState = pageState || {};

  var nodeModel = toura.app.Data.getModel(nodeId),
      page = toura.app.UI.getCurrentPage();

  if (!nodeModel) {
    toura.app.Router.home();
    return;
  }

  if (!page || !page.node || nodeId !== page.node.id) {
    page = toura.app.PageFactory.createPage('node', nodeModel);

    if (page.failure) {
      toura.app.Router.back();
      return;
    }

    toura.app.UI.showPage(pf, nodeModel);
  }

  page.init(pageState);

  // record node pageview if it is node-only
  if (nodeId && !pageState.assetType) {
    dojo.publish('/node/view', [ route.hash ]);
  }

  return true;
}
{% endcodeblock %}

The router observes a URL change, parses the parameters for the route from the
URL, and passes those parameters to a function. The Data component gets the
relevant data, and then hands it to the PageFactory component to generate the
page. As the page is generated, the individual components for the page are also
created and placed in the page. The PageFactory component returns the generated
page, but at this point the page is not in the DOM. The UI component receives
it, places it in the DOM, and handles the animation from the old page to the
new one.

Every step is its own tiny app, making the whole process tremendously testable.
The output of one step may become the input to another step, but when input and
output are predictable, the questions our tests need to answer are trivial:
“When I asked the Data component for the data for node123, did I get the data
for node123?”

Individual UI components are their own tiny apps as well. On a page that
displays a videos node, we have a video player component, a video list
component, and a video caption component. Selecting a video in the list
announces the selection via the list’s onSelect method. Dojo allows us to
connect to the execution of object methods, so in the page controller, we have
this:

{% codeblock lang:javascript %}
this.connect(this.videoList, 'onSelect', function(assetId) {
  var video = this.\_videoById(assetId);
  this.videoCaption.set('content', video.caption || '');
  this.videoPlayer.play(assetId);
});
{% endcodeblock %}

The page controller receives the message and passes it along to the other
components that need to know about it — components don’t communicate directly
with one another. This means the component that lists the videos can list
anything, not just videos — its only job is to announce a selection, not to do
anything as a result.

## Keep rewriting

> It takes confidence to throw work away … When people first start drawing,
they’re often reluctant to redo parts that aren’t right … they convince
themselves that the drawing is not that bad, really — in fact, maybe they meant
it to look that way. - [Paul Graham, “Taste for Makers”](http://www.paulgraham.com/taste.html)

The blank slate offered by a rewrite allows us to fix old mistakes, but
inevitably we will make new ones in the process. As good stewards of our code,
we must always be open to the possibility of a better way of doing a thing. “It
works” should never be mistaken for “it’s done.”
