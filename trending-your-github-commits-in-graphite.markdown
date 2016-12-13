---
title: Trending your GitHub Commits in Graphite
date: 2012-07-22 22:45:00 -04:00
author: Jason Dixon
---

<p>Today I was browsing the list of service hooks that GitHub provides. I almost forgot that there's a <a href="https://help.github.com/articles/post-receive-hooks">simple WebHook service</a> that POSTs commit information during the git post-receive hook to any external URL. This got me thinking that it would be nice to trend commit activity inside my Graphite server. Don't get me wrong... GitHub already provides some really nice visualization for project and committer activity on their site. However, as a data junkie, I'd love to be able to correlate this activity with my own application  metrics.</p>

<p>This was a perfect fit for <a href="https://github.com/obfuscurity/backstop">Backstop</a>, the HTTP/JSON-to-Graphite bridge. After a couple hours of futzing around I had a working version. If you haven't used Backstop before, rest assured that getting started is pretty darn easy. In fact, if you're a Heroku customer, it's easy and <em>free</em>. There are just a few commands to get your own Backstop server running on Heroku.</p>

<!--readmore-->

<pre>
$ git clone https://github.com/obfuscurity/backstop.git
$ heroku create
$ git push heroku master
$ heroku config:add CARBON_URLS=graphite.example.com:2003
</pre>

<p>Next you'll want to visit the Admin page for your GitHub project and click the <em>Service Hooks</em> tab to view all of the available hooks. Go to the <em>WebHook URLs</em> service and add your URL (e.g. <tt>http://frilly-lace-2030.herokuapp.com/github</tt>. Click the <em>Test Hook</em> button if you want to fire off a test metric, but there are two things to take note of:

<ol style="font-size: 0.9em; line-height: 1.5em;">
<li style="margin-bottom: 10px;">The first time you try to connect to Backstop after launching it, the backend socket connection to Carbon might fail and log an exception, and</li>
<li>Test metrics are generated using older commit data; as such, you'll see the metric written to your Graphite server, but it will reflect the actual timestamp of the original commit.</li>
</ol>

<div style="text-align: center;  margin: 40px 0;"><img src="http://f.cl.ly/items/202B2i1v182Z1a3E2N3v/Screen%20shot%202012-07-22%20at%2010.48.12%20PM.png" width="600" /></div>

<p>That's basically all there is to it. Backstop will parse the JSON from GitHub and submit it as an <em>annotation-style</em> metric with a value of <tt>1</tt>. These can be visualized as vertical lines with Graphite's <tt><b>drawAsInfinite()</b></tt> function as demonstrated in my <a href="http://obfuscurity.com/2012/04/Unhelpful-Graphite-Tip-1">Unhelpful Graphite Tip #1</a>. If you want to trend all of the commits in a branch at once, just use a wildcard to aggregate the author field.</p>

<div style="text-align: center; margin: 40px 0;"><img src="http://f.cl.ly/items/111Q3Y2k05363K330Y0I/Screen%20shot%202012-07-22%20at%209.46.20%20PM.png" /></div>