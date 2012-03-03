--- 
layout: post
date: "2008-09-22"
title: Notes on handing off a design to a front-end developer
comments: true
categories: freelancing, front-end development
---

I've been spending a lot of time lately turning other people's designs into working websites, and often there are a few rounds of back-and-forth before I have everything I need. Some notes on must-have pieces before I can begin work:

<strong>Design</strong>
<ul>
	<li>
<strong>Custom fonts. </strong>If the design includes fonts that aren't on <a href="http://www.ampsoft.net/webdesign-l/WindowsMacFonts.html">this list</a>, then either the designer needs to provide a PSD that has all instances of the font being used in the design, or else I'm going to need the font file in order to create them. (There may be <a href="http://clagnut.com/blog/1798/">licensing</a> <a href="http://www.fsf.org/blogs/licensing/20050425novalis">issues</a> here, which I leave as an exercise for the reader. In print land, we were allowed to provide the print shop with the fonts required to produce a printed piece, and the print shop was not supposed to retain them once the print run was complete. I imagine something similar applies to web production.)</li>
	<li>
<strong>Navigation state information. </strong>What should navigation elements look like in the up, over, and current states? Ideally I'd like to see all of the buttons in each state, but if the variations are simple then I can probably make them myself.</li>
	<li>
<strong>Link state information.</strong> What color should links be? Should they be underlined all the time, or only on hover? What color should they be when they are hovered? When they've already been visited?</li>
	<li>
<strong>Form treatment.</strong> If the site has any forms on it, what should form elements -- text inputs, submit buttons and input labels -- look like?</li>
	<li>
<strong>Typography specifications.</strong> Decisions about these can dramatically affect the feel of the site, and you may not want to leave them up to me. Specifically:
<ul>
	<li>How big should body copy be?</li>
	<li>How much space should there be between body copy lines? (Normally the "line-height" is set at 1.2 times the size of the font; increasing this can make body copy more legible.)</li>
	<li>How much space should there be between body copy paragraphs?</li>
	<li>How should headings be styled? How big should primary and secondary headlines be relative to the body copy?</li>
	<li>How should lists (ordered and bulleted) be styled?</li>
</ul>
</li>
	<li>
<strong>Imagery specifications.</strong> Like typography specifications, these considerations should be made carefully, as they affect the feel of the site. Specifically:
<ul>
	<li>How should text wrap around an image? Should the image float to the left, with copy wrapping around the right? Float to the right, copy on the left? Leave it up to the user?</li>
	<li>If an image is floated to the left or right, how much space should be between it and the text?</li>
</ul>
</li>
</ul>
<strong>Documentation</strong>
<ul>
	<li>
<strong>Approved content documents.</strong> Personally I prefer a single content document per static page that needs to be created, and a content sample for any dynamic pages that will need to be created. If there are any repeating elements in the design that require content, I'd enjoy getting a document that contains their text as well. Lastly, if there are any dynamically generated content items -- for example, a list of latest posts -- I'd like a document that shows me how these should be formatted. This will help me:
<ul>
	<li>Make sure I've accounted for the pieces of the content and how they fit together.</li>
	<li>Know that all content has been considered, reviewed and approved, so there shouldn't be too many surprises later.</li>
	<li>Reconcile the content documents with any other documentation I've received.</li>
</ul>
</li>
	<li>
<strong>Functional spec document. </strong>Let me know what should happen when someone fills out a form. Let me know how the items in the sidebar should be gathered. Let me know where that video's going to come from. Basically, anytime I ask "Where does that come from?" or "What does this do?", the functional spec should answer that question. While a functional spec should be initiated long before the design is complete, it is good to review the design and make sure those questions are answered. A functional spec should also include a sitemap.</li>
</ul>