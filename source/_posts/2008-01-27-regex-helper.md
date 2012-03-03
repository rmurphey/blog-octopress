--- 
layout: post
date: "2008-01-27"
title: Regex helper
comments: true
categories: regex
---

Update: The original link is now dead. Here's another <a href="http://bit.ly/2K8TKT">regex tester</a> to try.

I recently had to come up with a regular expression that I could run against an HTML string that would only affect content, not tags or attribute values. I'm no regex master, so after a whole lot of failed attempts, I turned to the Google and found this <a href="http://nancywalshee03.freehostia.com/regextester/regex_tester.php?seeSaved=uo23fkfh"><del datetime="2009-03-03T23:46:38+00:00">invaluable regex tester</del></a>, which also had a bunch of other problems already solved (click on the Samples link at the top). Here's the answer to my particular question that day:

<div class="CodeRay">
  <div class="code"><pre>/(?![^)\b(text_to_match)\b/</pre></div>
</div>


I hate when it's that simple.