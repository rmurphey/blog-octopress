---
layout: post
title: "Mulberry: A development framework for mobile apps"
date: 2011-09-18 11:30
comments: true
categories:
---

I'll be getting on stage in a bit at [CapitolJS](http://capitoljs.com), another
great event from Chris Williams, the creator of JSConf and all-around
conference organizer extraordinaire. My schtick at conferences in the past has
been to talk about the pain and pitfalls of large app development with
JavaScript, but this time is a little different: I'll be announcing that [Toura Mobile](http://toura.com) has created a framework built on top of PhoneGap that
aims eliminate some of those pains and pitfalls for mobile developers. We're
calling it [Mulberry](http://mulberry.toura.com), and you'll be seeing it on GitHub in the next few weeks.

**tl;dr**: [go here](http://mulberry.toura.com) and [watch this video](http://mulberry.toura.com/#tour).

While the lawyers are dotting the i's and crossing the t's as far as getting the code in your hands -- we're aiming for a permissive license similar to the licenses for PhoneGap and Dojo -- I wanted to tell you a little bit about it.

Mulberry is two things. First, it's command line tools (written in Ruby) that help you rapidly scaffold and configure an app, create content using simple Markdown and YAML, and test it in your browser, in a simulator, and on device. Second, and much more exciting to me as a JavaScript developer, it's a framework built on top of the Dojo Toolkit for structuring an application and adding custom functionality in a sane way.

Mulberry lets you focus on the things that are unique to your application. It provides an underlying framework that includes a "router" for managing application state; built-in components and templates for displaying standard content types like text, audios, videos, feeds, and images; a simple API for defining custom functionality and integrating it with the system; and an HTML/CSS framework that uses SASS and HAML templates to make it easy to style your apps.

The basics of setting up an app are pretty well covered at the [Mulberry
site](http://mulberry.toura.com), but if you're reading this, you're probably a JavaScript developer, so I want to focus here on what Mulberry can do for you. First, though, let me back up and cover some terminology: Mulberry apps consist of a set of "nodes"; each node is assigned a template, and each template consists of components arranged in a layout. Nodes can have assets associated with them -- text, audio, images, video, feeds, and data.

It's the data asset that provides the most power to developers -- you can create an arbitrary object, associate it with a node, and then any components that are in the template that's being used to display the node will get access to that data.

A Twitter component offers a simple example. A node might have a data asset like this associated with it:

{% codeblock lang:javascript %}
{ term : 'capitoljs', type : 'twitter' }
{% endcodeblock %}

We could define a custom template for this page (`mulberry create_template Twitter`), and tell that template to include a Twitter component:

{% codeblock lang:yaml %}
Twitter:
  screens:
    - name: index
      regions:
        -
          size: fixed
          scrollable: false
          components:
            - PageNav
        -
          size: flex
          scrollable: true
          components:
            - PageHeaderImage
            - custom:Twitter
{% endcodeblock %}

Next, we'd define our Twitter component (`mulberry create_component Twitter`), which would create the skeleton of a component file:

{% codeblock lang:javascript %}
dojo.provide('client.components.Twitter');

toura.component('Twitter', {
  componentTemplate : dojo.cache('client.components', 'Twitter/Twitter.haml'),

  prep : function() {

  },

  init : function() {

  }
});
{% endcodeblock %}

One of the things the skeleton contains is a reference to the template for the component. The `create_component` command creates this file, which defines the DOM structure for the component. For the sake of this component, that template will just need to contain one line:

{% codeblock lang:haml %}
%ul.component.twitter
{% endcodeblock %}

As I mentioned earlier, Mulberry components automatically get access to all of the assets that are attached to the node they're displaying. This information is available as an object at `this.node`. Mulberry components also have two default methods that you can implement: the `prep` method and the `init` method.

The `prep` method is an opportunity to prepare your data before it's rendered using the template; we won't use it for the Twitter component, because the Twitter component will go out and fetch its data *after* the template is rendered. This is where the `init` method comes in -- this is where you can tell your component what to do. Here's what our Twitter component ends up looking like:

{% codeblock lang:javascript %}
dojo.provide('client.components.Twitter');

mulberry.component('Twitter', {
  componentTemplate : dojo.cache('client.components', 'Twitter/Twitter.haml'),
  tweetTemplate : dojo.cache('client.components', 'Twitter/Tweet.haml'),

  init : function() {
    var data = dojo.filter(this.node.data, function(d) {
          return d.type === 'twitter'
        })[0].json;

    $.ajax('http://search.twitter.com/search.json?q=' + data.term, {
      dataType : 'jsonp',
      success : $.proxy(this, '_onLoad')
    });
  },

  _onLoad : function(data) {
    var tweets = data.results,
        tpl = mulberry.haml(this.tweetTemplate),
        html = $.map(tweets, function(tweet) {
          tweet.link = 'http://twitter.com/capitoljs/status/' + tweet.id_str;

          tweet.created_at = dojo.date.locale.format(
            new Date(tweet.created_at), {
              datePattern : 'EEE',
              timePattern : 'h:m a'
            }
          );

          tweet.text = tweet.text.replace(
            /@(\S+)/g,
            "<a href='http://twitter.com/#!/$1'>@$1</a>"
          );

          return tpl(tweet);
        }).join('');

    this.$domNode.html(html);
    this.region.refreshScroller();
  }
});
{% endcodeblock %}

Note that when we define the `data` variable in the init method, we look at `this.node.data`, which is an array of all of the data objects associated with the node. We filter this array to find the first data object that is the right type -- this means we can have lots of different data objects associated with a given node.

Note also that there's a property `this.$domNode` that we're calling jQuery methods on, and that we're using jQuery's `$.ajax` -- Mulberry apps come with jQuery enabled by default, and if it's enabled, helpers like `this.$domNode` become available to you. This means that very little knowledge of Dojo is required to start adding your own functionality to an app -- if you need it, though, the full power of the Dojo Toolkit is available to you too.

Here's what our component ends up looking like, with a little bit of custom CSS applied to our app:

![screenshot](http://www.rebeccamurphey.com/i/4e760b2ff3b5b.jpg "A Mulberry app using a custom Twitter component")

This is a pretty basic demo -- Twitter is, indeed, the new `hello world` -- but I hope it gives you a little bit of an idea about what you might be able to build with Mulberry. We've been using it in production to create content-rich mobile apps for our users for months now (connected to a web-based CMS instead of the filesystem, of course), and we've designed it specifically to be flexible enough to meet arbitrary client requests without the need to re-architect the underlying application.

If you know JavaScript, HTML, and CSS, Mulberry is a powerful tool to rapidly create a content-rich mobile application while taking advantage of an established infrastructure, rather than building it yourself. I'm excited to see what you'll do with it!
