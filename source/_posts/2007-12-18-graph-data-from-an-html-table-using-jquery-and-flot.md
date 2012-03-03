--- 
layout: post
date: "2007-12-18"
title: Graph data from an HTML table using jQuery and flot
comments: true
categories: flot, front-end development, graph, javascript, jquery, plugins, table, tabular data
---

<em>NOTE: A couple of commenters have pointed out that this plugin has issues in IE. I haven't had time to look into it, so I'm not sure whether they are related to my plugin or to flot. Make sure you read <a href="http://flot.googlecode.com/svn/trunk/README.txt">flot's readme</a> about getting flot working in IE, I think it will address most IE issues.</em>

<em>NOTE 2: I've posted a <a href="http://plugins.jquery.com/files/jquery.graphTable-0.2.js.txt">minor update to graphTable</a> to remove an errant console.log call.</em>

<em>NOTE 3: I've changed the license on the plugin; it is now dual-licensed MIT and GPL, like jQuery itself.</em>

I just got done with a first draft of the <a href="http://plugins.jquery.com/files/jquery.graphTable-0.2.js.txt">graphTable</a> plugin, which lets you take a simple HTML table and turn the data in it into a graph using jQuery and <a href="http://code.google.com/p/flot/">flot</a>. Here's a <a href="http://rebeccamurphey.com/jquery/graphTable/table.html">demo</a>, and here's the <a href="http://plugins.jquery.com/project/graphTable">jQuery plugin page</a> where I'll continue development if there's any interest.

The most basic usage is simple:
<div class="CodeRay">
  <div class="code"><pre>$('#table1').graphTable({series: 'columns'});</pre></div>
</div>

<div class='p_embed p_image_embed'>
<img alt="" src="http://blog.rebeccamurphey.com/wp-content/uploads/2007/12/graphtable.jpg" />
</div>


graphTable() takes up to two objects as arguments: the first is an object with the arguments for graphTable; the second is an object with arguments to be handed off to flot (<a href="http://flot.googlecode.com/svn/trunk/API.txt">read more about flot arguments</a>).

The graphTable arguments let you:
<ul>
	<li>choose which parts of the table will be used for the graph data</li>
	<li>choose where the graph will go in relation to the table and how big it will be</li>
	<li>run a function on cell contents (to remove dollar signs, for example) before passing them to flot</li>
</ul>
Each of the following options can be set as part of an object passed to graphTable as the first argument. For example:
<div class="CodeRay">
  <div class="code"><pre>$('#table1').graphTable({
  series: 'columns',
  dataTransform: function(s) { return(s.replace('$','')); }
});</pre></div>
</div>

<h3>options for reading the table</h3>
defaults will work in most cases except you'll want to override the default args.series if your series are in columns
<ul>
	<li>
<strong>series</strong> 'rows', 'columns'</li>
	<li>
<strong>labels</strong> integer index of the cell in the series row/column that contains the label for the series; default is 0</li>
	<li>
<strong>xaxis</strong> integer index of the row/column (whatever args.series is) that contains the x values; default is 0</li>
	<li>
<strong>firstSeries</strong> index of the row/column containing the first series; default is 1</li>
	<li>
<strong>lastSeries</strong> index of the row/column containing the last series; will use the last cell in the row/col if not set; default is null</li>
	<li>
<strong>dataStart</strong> index of the first cell in the series containing data; default is 1</li>
	<li>
<strong>dataEnd</strong> index of the last cell in the series containing data; will use the last cell in the row/col if not set; default is null</li>
</ul>
<h3>graph size and position</h3>
<ul>
	<li>
<strong>position</strong> 'before', 'after', 'replace'; indicate whether the graph should go before the table, go after the table, or replace the table</li>
	<li>
<strong>width</strong> leave as null to use the width of the table for the width of the graph; otherwise, set to a width in pixels</li>
	<li>
<strong>height</strong> leave as null to use the height of the table for the height of the graph; otherwise, set to a height in pixels</li>
</ul>
<h3>data transformation before plotting</h3>
<ul>
	<li>
<strong>dataTransform</strong> function to run on cell contents before passing to flot; string -&gt; string</li>
	<li>
<strong>labelTransform</strong> function to run on cell contents before passing to flot; string -&gt; string</li>
	<li>
<strong>xaxisTransform</strong> function to run on cell contents before passing to flot; string -&gt; string</li>
</ul>