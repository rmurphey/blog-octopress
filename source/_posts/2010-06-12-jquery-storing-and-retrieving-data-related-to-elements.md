--- 
layout: post
date: "2010-06-12"
title: "jQuery: Storing and retrieving data related to elements"
comments: true
categories: data, howto, html5, javascript, jquery
---

<p>It&rsquo;s very common to need to get information about a DOM element when a user interacts with it &mdash; for example, perhaps you have an unordered list of names, and when a user clicks on a name, you want to show a picture of the person above the list. In order to do this, you need to figure out which person the clicked list item represents.  Many beginning jQuery users will attempt to achieve this by putting ID attributes on each list item, such as <code>id="rebecca"</code>. Then, they&rsquo;ll read the ID attribute off the clicked element and use it to build a URL for the related image.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="tag">&lt;ul</span> <span class="attribute-name">class</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">people</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>
<span class="tag">&lt;li</span> <span class="attribute-name">id</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">paul</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>Paul<span class="tag">&lt;/li&gt;</span>
<span class="tag">&lt;li</span> <span class="attribute-name">id</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">rebecca</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>Rebecca<span class="tag">&lt;/li&gt;</span>
<span class="tag">&lt;li</span> <span class="attribute-name">id</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">alex</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>Alex<span class="tag">&lt;/li&gt;</span>
<span class="tag">&lt;li</span> <span class="attribute-name">id</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">adam</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>Adam<span class="tag">&lt;/li&gt;</span>
<span class="tag">&lt;/ul&gt;</span>


<span class="keyword">var</span> portrait = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#portrait</span><span class="delimiter">'</span></span>);

<span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">ul.people li</span><span class="delimiter">'</span></span>).click(<span class="keyword">function</span>() {
    <span class="keyword">var</span> name = <span class="predefined">$</span>(<span class="local-variable">this</span>).attr(<span class="string"><span class="delimiter">'</span><span class="content">id</span><span class="delimiter">'</span></span>);
    portrait.html(<span class="string"><span class="delimiter">'</span><span class="content">&lt;img class=&quot;posterous_download_image&quot; src=&quot;/images/people/</span><span class="delimiter">'</span></span>%<span class="integer">20</span>+%<span class="integer">20</span>name%<span class="integer">20</span>+%<span class="integer">20</span><span class="string"><span class="delimiter">'</span><span class="content">.jpg&quot; alt=&quot;&quot; /&gt;</span><span class="delimiter">'</span></span>);
});</pre></div>
</div>


<p>Strictly speaking, this will work. But is the ID really the right place to store this information? What if you need this behavior on other elements on the page too? You can&rsquo;t have more than one element with the same ID on the page, so you might find yourself using funny prefixes in your IDs, like <code>person_rebecca</code>, and then stripping out the prefix. You could do it with classes, but then you&rsquo;d have the opposite problem: you&rsquo;re using (generally) unique classes like <code>rebecca</code>, but really classes are meant to indicate similarities among a set of elements. And what if you need to store more than one piece of information about an element on the element? Next thing you know you&rsquo;ve got <code>id="person_alex_red"</code> and you&rsquo;re jumping through all sorts of hoops to parse out the data you need.</p>

<h3>Custom Data Attributes</h3>

<p>HTML5 makes available [custom data attributes](<a href="http://dev.w3.org/html5/spec/Overview.html#custom-data-attribute">http://dev.w3.org/html5/spec/Overview.html#custom-data-attribute</a>, and they prove to be a much more elegant and robust solution to this problem. They&rsquo;re custom, so they can contain pretty much anything you want, and each element can have as many of them as you want.</p>

<div class="CodeRay">
  <div class="code"><pre><span class="tag">&lt;ul</span> <span class="attribute-name">class</span>=<span class="string"><span class="delimiter">&quot;</span><span class="content">people</span><span class="delimiter">&quot;</span></span><span class="tag">&gt;</span>
  <span class="tag">&lt;li&gt;</span>Paul<span class="tag">&lt;/li&gt;</span>
  <span class="tag">&lt;li&gt;</span>Rebecca<span class="tag">&lt;/li&gt;</span>
  <span class="tag">&lt;li&gt;</span>Alex<span class="tag">&lt;/li&gt;</span>
  <span class="tag">&lt;li&gt;</span>Adam<span class="tag">&lt;/li&gt;</span>
<span class="tag">&lt;/ul&gt;</span>


<span class="keyword">var</span> <span class="predefined">$portrait</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#portrait</span><span class="delimiter">'</span></span>);

<span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">ul.people li</span><span class="delimiter">'</span></span>).click(<span class="keyword">function</span>() {
    <span class="keyword">var</span> <span class="predefined">$li</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>),
        name = <span class="predefined">$li</span>.attr(<span class="string"><span class="delimiter">'</span><span class="content">data-name</span><span class="delimiter">'</span></span>),
        color = <span class="predefined">$li</span>.attr(<span class="string"><span class="delimiter">'</span><span class="content">data-hairColor</span><span class="delimiter">'</span></span>),
        <span class="predefined">$img</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;img class=&quot;posterous_download_image&quot; src=&quot;/images/people/</span><span class="delimiter">'</span></span>%<span class="integer">20</span>+%<span class="integer">20</span>name%<span class="integer">20</span>+%<span class="integer">20</span><span class="string"><span class="delimiter">'</span><span class="content">.jpg&quot; alt=&quot;&quot; /&gt;</span><span class="delimiter">'</span></span>);

    <span class="predefined">$portrait</span>.append(<span class="predefined">$img</span>).css(<span class="string"><span class="delimiter">'</span><span class="content">border</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">5px solid </span><span class="delimiter">'</span></span> + color);
});</pre></div>
</div>


<h3>$.fn.data</h3>

<p>When you want to embed information about an element in the HTML you send down from your server, custom data attributes offer a clear and easy solution. But what if you want to attach data to elements that you&rsquo;ve added to the page using JavaScript? For example, you might have some data you fetched from your server via an Ajax request:</p>

<div class="CodeRay">
  <div class="code"><pre>{
    <span class="key"><span class="delimiter">&quot;</span><span class="content">items</span><span class="delimiter">&quot;</span></span> : [
        { <span class="key"><span class="delimiter">&quot;</span><span class="content">name</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">Paul</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">image</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">paul</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">hairColor</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">black</span><span class="delimiter">&quot;</span></span> },
        { <span class="key"><span class="delimiter">&quot;</span><span class="content">name</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">Rebecca</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">image</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">rebecca</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">hairColor</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">brown</span><span class="delimiter">&quot;</span></span> },
        { <span class="key"><span class="delimiter">&quot;</span><span class="content">name</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">Alex</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">image</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">alex</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">hairColor</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">red</span><span class="delimiter">&quot;</span></span> },
        { <span class="key"><span class="delimiter">&quot;</span><span class="content">name</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">Adam</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">image</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">adam</span><span class="delimiter">&quot;</span></span>, <span class="key"><span class="delimiter">&quot;</span><span class="content">hairColor</span><span class="delimiter">&quot;</span></span> : <span class="string"><span class="delimiter">&quot;</span><span class="content">red</span><span class="delimiter">&quot;</span></span> }
    ]
}</pre></div>
</div>


<p>You&rsquo;re going to iterate over the data to produce a structure much like the one above, but in this case, it doesn&rsquo;t make sense to store the related data in markup, because you&rsquo;ll just have to extract it again later. Instead, you can use the <code>$.fn.data()</code> method in jQuery to store the data using JavaScript instead of markup:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> <span class="predefined">$target</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">ul.people</span><span class="delimiter">'</span></span>);

<span class="predefined">$</span>.each(response.items, <span class="keyword">function</span>(i, data) {
    <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;li/&gt;</span><span class="delimiter">'</span></span>, { <span class="key">html</span> : data.name })
      .data({
        <span class="key">name</span> : data.image,
        <span class="key">hairColor</span>: data.hairColor
      })
      .appendTo(<span class="predefined">$target</span>);
});</pre></div>
</div>


<p>Later, you can read the data off the element using the <code>$.fn.data()</code> method again, this time passing just the name of the key you&rsquo;re after:</p>

<div class="CodeRay">
  <div class="code"><pre><span class="keyword">var</span> <span class="predefined">$portrait</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">#portrait</span><span class="delimiter">'</span></span>);

<span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">ul.people li</span><span class="delimiter">'</span></span>).click(<span class="keyword">function</span>() {
    <span class="keyword">var</span> <span class="predefined">$li</span> = <span class="predefined">$</span>(<span class="local-variable">this</span>),
        name = <span class="predefined">$li</span>.data(<span class="string"><span class="delimiter">'</span><span class="content">name</span><span class="delimiter">'</span></span>),
        color = <span class="predefined">$li</span>.data(<span class="string"><span class="delimiter">'</span><span class="content">hairColor</span><span class="delimiter">'</span></span>),
        <span class="predefined">$img</span> = <span class="predefined">$</span>(<span class="string"><span class="delimiter">'</span><span class="content">&lt;img class=&quot;posterous_download_image&quot; src=&quot;/images/people/</span><span class="delimiter">'</span></span>%<span class="integer">20</span>+%<span class="integer">20</span>name%<span class="integer">20</span>+%<span class="integer">20</span><span class="string"><span class="delimiter">'</span><span class="content">.jpg&quot; alt=&quot;&quot; /&gt;</span><span class="delimiter">'</span></span>);

    <span class="predefined">$portrait</span>.append(<span class="predefined">$img</span>).css(<span class="string"><span class="delimiter">'</span><span class="content">border</span><span class="delimiter">'</span></span>, <span class="string"><span class="delimiter">'</span><span class="content">5px solid </span><span class="delimiter">'</span></span> + color);
});</pre></div>
</div>


<h3>Mixing the two methods</h3>

<p>This is all well and good, but what if you have a list of people that was sent from the server using HTML and custom data attributes, and then you add elements to it later using JavaScript and store data on them using <code>$.fn.data()</code>? Now your data is stored on elements in two different ways, so how do you extract it reliably?  One option is to handle both cases. First, you&rsquo;ll switch to using jQuery&rsquo;s <a href="http://api.jquery.com/delegate/">delegate method</a> for the event binding, so you don&rsquo;t have to keep binding click handlers as you add list items to your list. Then, inside of your click handler, you&rsquo;ll figure out where you can get your data from:</p>

<div class="CodeRay">
  <div class="code"><pre>var $portrait = $('#portrait');

$('ul.people').delegate('li', 'click', function() {
    var $li = $(this),
        name = $li.attr('data-name'),
        color, $img;

    if (!name) { // did the li have custom data attributes?
        name = $li.data('name');
        color = $li.data('hairColor');
    } else {
        color = $li.attr('data-hairColor');
    }

    $img = $('&lt;img class=&quot;posterous_download_image&quot; src=&quot;/images/people/'%20+%20name%20+%20'.jpg&quot; alt=&quot;&quot; /&gt;');
    $portrait.append($img).css('border', '5px solid ' + color);
});</pre></div>
</div>


<p>Another option is to iterate over the original elements, and store the data from the custom data attributes using the <code>$.fn.data()</code> method:</p>

<div class="CodeRay">
  <div class="code"><pre>var $portrait = $('#portrait'),
      $ul = $('ul.people');

$ul.find('li').each(function() {
    var $li = $(this);
    $li.data({
        name : $li.attr('data-name'),
        hairColor : $li.attr('data-hairColor')
    });
});

$('ul.people').delegate('li', 'click', function() {
    var $li = $(this),
        name = $li.data('name'),
        color = $li.data('hairColor'),
        $img = $('&lt;img class=&quot;posterous_download_image&quot; src=&quot;/images/people/'%20+%20name%20+%20'.jpg&quot; alt=&quot;&quot; /&gt;');

    $portrait.append($img).css('border', '5px solid ' + color);
});

// load more list items via ajax at some point
// to make that whole delegate thing worthwhile</pre></div>
</div>


<p>Which option you use will depend on how large your original list is (and thus how long it will take to iterate over it), and how likely people are to click on a lot of items in the list (and thus whether the initial iteration is worth the time). I leave it as an exercise for the reader to decide which approach makes sense for you.</p>

<h3>In Conclusion</h3>

<p>When you need to attach data to elements and then extract that data later, there are options beyond classes and IDs, and in fact classes and IDs may be an especially poor way to approach the problem. Taking advantage of custom data attributes and the <code>$.fn.data()</code> method in jQuery can make it painless to store and retrieve data related to elements, and the metadata plugin can streamline the process for you even further.</p>

<h3>Further Reading</h3>

<ul>
<li><a href="http://docs.jquery.com/Plugins/Metadata">The jQuery Metadata plugin</a></li>
</ul>