--- 
layout: post
date: "2008-06-03"
title: Track user clicks on certain links
comments: true
categories: analytics, howto, jquery, plugins
---

We recently added a new feature to <a href="http://dailystrength.org">dailystrength.org</a>, and there was much debate about whether users would use it as we intended. To find out, I wrote a clicktrack ditty that sends some data to the server when a link is clicked. It was somewhat inspired by a post I wrote a while ago about <a href="http://blog.rebeccamurphey.com/2008/01/06/track-outbound-clicks-with-google-analytics-and-jquery/">tracking outbound links with Google Analytics and jQuery</a>, but is actually even simpler. I've retooled my clicktrack script to work as a generic, 2.5k plugin, which you can download <a href="http://rebeccamurphey.com/jquery/clicktrack/jquery.clicktrack.js">here</a>.
<h3>Usage</h3>
To use this plugin, add a distinct classname to all links you would like to track. For example:
<div class="CodeRay">
  <div class="code"><pre>this is a link</pre></div>
</div>

You can also add additional classes to a link with a distinct prefix — the default prefix is "ct_". These classes will be passed to the server along with other clicktrack data:
<div class="CodeRay">
  <div class="code"><pre>this is a link</pre></div>
</div>

Then, include the script and a line of code to initialize it in the foot of your page. Note that you must at least pass a URL for the remote (server-side) script that will handle the clicktrack data ("foo.php" in the example below):
<div class="CodeRay">
  <div class="code"><pre>type=&quot;text/javascript&quot;&gt;

$(document).ready(function() { $(&quot;a.clicktrack&quot;).clicktrack(&quot;foo.php&quot;); });</pre></div>
</div>

This will pass the following data to a script called "foo.php" on your server:
<ul>
	<li>
<strong>source (string)</strong> the URL of the current page</li>
	<li>
<strong>target (string)</strong> the HREF of the clicked link</li>
	<li>
<strong>classes (array)</strong> classes on the link that matched the prefix (default prefix is "ct_")</li>
</ul>
<h3>Optional parameters</h3>
The only required parameter is the URL of the remote script. However, you can pass a more elaborate configuration object. The options and their defaults are as follows:
<div class="CodeRay">
  <div class="code"><pre>var defaults = {
                remote_script: null,
                prefix: 'ct_',
                extraData: null,
                callback: function(){},
                dataType: null,
                sendOnce: true,
                preventDefault: false
        };</pre></div>
</div>

<ul>
	<li>
<strong>remote_script</strong> the remote script that will handle the clicktrack data. this can be passed as a single, string argument to clicktrack(), or it can be passed as part of the configuration object, <em>but it must be specified</em>
</li>
	<li>
<strong>prefix</strong> the prefix for classnames that should be passed as part of the clicktrack data. defaults to "ct_".</li>
	<li>
<strong>extraData</strong> additional data that should be passed as part of the clicktrack data.</li>
	<li>
<strong>callback</strong> function to be executed upon successful execution of the remote script. you'll need to set <strong>preventDefault</strong> to true for this callback to get executed, and then redirect the user using <code>window.location</code> within the callback as required.</li>
	<li>
<strong>dataType</strong> type of data you expect to be returned by the remote script. see <a href="http://docs.jquery.com/Ajax/jQuery.post#urldatacallbacktype">the jQuery documentation on $.post() for details</a>.</li>
	<li>
<strong>sendOnce</strong> whether clicktrack data for a given link should be sent just once. defaults to true.</li>
	<li>
<strong>preventDefault</strong> whether clicks on clicktrack links should be prevented from taking the user to the link destination. defaults to false (links will work as expected by default).</li>
</ul>