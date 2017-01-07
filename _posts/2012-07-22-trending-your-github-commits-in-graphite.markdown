---
title: Trending your GitHub Commits in Graphite
date: 2012-07-22 22:45:00 -04:00
author: Jason Dixon
excerpt: "This got me thinking that it would be nice to trend GitHub commit activity inside my Graphite server."
categories:
tags: ["github", "open source", "graphite", "graph", "trending", "visualization"]
image:
  feature: github-library-octocat.jpg
  topPosition: -50px
---

Today I was browsing the list of service hooks that GitHub provides. I almost forgot that there's a [simple WebHook service](https://help.github.com/articles/post-receive-hooks) that POSTs commit information during the git post-receive hook to any external URL. This got me thinking that it would be nice to trend commit activity inside my Graphite server. Don't get me wrong... GitHub already provides some really nice visualization for project and committer activity on their site. However, as a data junkie, I'd love to be able to correlate this activity with my own application  metrics.

This was a perfect fit for [Backstop](https://github.com/obfuscurity/backstop), the HTTP/JSON-to-Graphite bridge. After a couple hours of futzing around I had a working version. If you haven't used Backstop before, rest assured that getting started is pretty darn easy. In fact, if you're a Heroku customer, it's easy and *free*. There are just a few commands to get your own Backstop server running on Heroku.

```
$ git clone https://github.com/obfuscurity/backstop.git
$ heroku create
$ git push heroku master
$ heroku config:add CARBON_URLS=graphite.example.com:2003
```

Next you'll want to visit the Admin page for your GitHub project and click the *Service Hooks* tab to view all of the available hooks. Go to the *WebHook URLs* service and add your URL (e.g. `http://frilly-lace-2030.herokuapp.com/github`. Click the *Test Hook* button if you want to fire off a test metric, but there are two things to take note of:

1. The first time you try to connect to Backstop after launching it, the backend socket connection to Carbon might fail and log an exception, and
1. Test metrics are generated using older commit data; as such, you'll see the metric written to your Graphite server, but it will reflect the actual timestamp of the original commit.

![GitHub WebHook URL, maxwidth](http://f.cl.ly/items/202B2i1v182Z1a3E2N3v/Screen%20shot%202012-07-22%20at%2010.48.12%20PM.png)

That's basically all there is to it. Backstop will parse the JSON from GitHub and submit it as an *annotation-style* metric with a value of `1`. These can be visualized as vertical lines with Graphite's **`drawAsInfinite()`** function as demonstrated in my [Unhelpful Graphite Tip #1](http://obfuscurity.com/2012/04/Unhelpful-Graphite-Tip-1). If you want to trend all of the commits in a branch at once, just use a wildcard to aggregate the author field.

![Graphite metric tree, maxwidth](http://f.cl.ly/items/111Q3Y2k05363K330Y0I/Screen%20shot%202012-07-22%20at%209.46.20%20PM.png)
