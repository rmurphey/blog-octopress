--- 
layout: post
date: "2009-04-13"
title: On gaining respect as a front-end developer
comments: true
categories: front-end development, thoughts
---

Someone wrote me today:

<blockquote class="posterous_medium_quote">Where I work, design is highly valued with the leader of that group being our Creative Director, back end programmers are also highly valued, but front end ... not so much. Partly I think its that I don't toot my horn but I know there are other reasons. At times, my bosses haven't even understood what it is that I do. Back end programmers look down on front end assuming that its trivial or something that should be relegated to compilers.

I was wondering if this is a common thing or more so something that is happening at my particular company, and if you have any advice or pointers on this.</blockquote>

I thought my response might be worth sharing:

I do think this attitude is common but not necessarily the rule. In my experience, I've found that by having a proven value proposition, you can gain converts and respect.

Front end developers are in a unique position to improve page performance (perceived and actual) by using best practices such as the <a href="http://developer.yahoo.com/yslow/">YSlow</a> tests. Front end developers are also in a unique position to help develop templating systems and to write thoughtful CSS, both of which help enable the rapid prototyping and rollout of new features. A focus on results and best practices -- demonstrating that you aren't just pushing pixels around -- is the key.

Back end developers respect people who think like they do. Be mindful of opportunities for abstraction and reuse. Write <a href="http://www.slideshare.net/stubbornella/object-oriented-css">object-oriented CSS</a> and JavaScript. Craft solutions that are maintainable and documented. Learn and make use of <a href="http://www.kernel.org/pub/software/scm/git/docs/gittutorial.html">version control</a> <a href="http://svnbook.red-bean.com/nightly/en/svn.intro.quickstart.html">systems</a>. Look for opportunities to participate in developer conversations about new features, and understand what the back end developers are up against. They'll appreciate all of this.

Take the time to teach and to learn. Be sure you have at least a passing understanding of the code the back end developers are writing, and leap at opportunities to share your knowledge. I've worked with more than one back end developer who was surprised to discover what all they didn't know about the front end, and through our conversations about how we approached problems, we both learned a lot.

Finally: identify opportunities for quick victories, execute on them, and make the results known. Benchmark before and after. Can you reduce the number of HTTP requests on a page, decreasing both the perceived and actual rendering time? Are you keeping your JavaScript out of the &lt;head&gt; as much as possible, preventing pages from stalling while rendering? Can you write JavaScript that is primed for reuse, and demonstrate opportunities for that reuse? Has your carefully crafted CSS allowed the rapid rollout of a new feature? Don't be afraid to tell these stories -- they'll tend to strengthen your position by clarifying the important role the front-end developer plays in a site.

Good luck :)