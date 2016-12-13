---
title: My Impressions of InfluxDB
date: 2013-11-11 12:15:00 -05:00
author: Jason Dixon
---

<p>I <a href="https://twitter.com/obfuscurity/status/398870783176765440">mentioned</a> last week that I was planning to look closer at InfluxDB this past weekend, and <a href="https://twitter.com/michaelgorsuch/status/398871267001892864">some</a> <a href="https://twitter.com/ryandotsmith/status/398875651484037120">folks</a> asked me to do a writeup on my findings.</p>

<p><a href="http://influxdb.org/">InfluxDB</a> is a time-series metrics and events database based on the <a href="https://code.google.com/p/leveldb/">LevelDB</a> key-value store. LevelDB was written and <a href="http://google-opensource.blogspot.com/2011/07/leveldb-fast-persistent-key-value-store.html">open sourced</a> by Google, and is an optional <a href="http://docs.basho.com/riak/latest/ops/advanced/backends/leveldb/">backend</a> for Riak. InfluxDB (or "Influx", for short) inherits many of LevelDB's default characteristics, which means it's optimized for writes and uses <a href="https://code.google.com/p/snappy/">compression</a> by default, but it can be slow for reads and deletes.</p>

<!--readmore-->

<p>Influx was only recently released as an <a href="https://github.com/influxdb/influxdb">open source project</a> under the MIT license by the team over at Errplane. Its authors are <a href="http://influxdb.org/overview/">transparent</a> about their goals and roadmap, publishing them on the project website.</p>

<p>Getting started with Influx is easy enough. They provide a free online <a href="http://play.influxdb.org/">sandbox</a> where you can create a user account and database, then immediately begin writing metrics via their administration UI. You can also start sending metrics remotely, which is simple enough with curl and a bit of JSON.</p>

<pre>
$ for i in `seq 100`; do \
>   curl -X POST "http://sandbox.influxdb.org:9061/db/foobar/series?u=foo&p=bar" \
>     -d "[{\"name\":\"data\",\"columns\":[\"foo\",\"bar\"], \
>     \"points\":[[$((RANDOM%200+100)),$((RANDOM%300+50))]]}]"; \
>   sleep 1; \
> done
</pre>

<p>From there you can run queries and visualize your metrics within the same interface.</p>

<div style="text-align: center;"><img style="margin: 40px 0;" src="https://s3.amazonaws.com/obfuscurity-blog/influxdb-ui.png" width=600 /></div>

<p>I don't care for their choice of accepting credentials via URL parameters, but it looks like they agree and <a href="https://github.com/influxdb/influxdb/issues/31">intend</a> to support HTTP Basic Auth soon. Otherwise, their HTTP API is largely param-driven, making it straightforward and easy to use, and is capable of sending chunked or non-chunked responses.</p>

<p>Influx accepts queries via an <a href="http://influxdb.org/docs/query_language/">SQL-like query language</a>, so if you're comfortable with traditional relational databases, you should feel right at home. It already supports filtering using <b><tt>where</tt></b> clauses, in addition to aggregates using <b><tt>group by</tt></b>, <b><tt>merge</tt></b> and <b><tt>join</tt></b>. There are a handful of mathematical functions (<tt>min</tt>, <tt>max</tt>, <tt>mean</tt>, <tt>mode</tt>, <tt>median</tt>, and <tt>percentile</tt>), making it suitable for routine analytics tasks.</p>

<p>They plan to add a feature called continuous queries, which will allow users to <em>&quot;precompute expensive queries into another time series in real-time&quot;</em>. At face value this sounds very much like Graphite's <a href="https://graphite.readthedocs.org/en/latest/carbon-daemons.html#carbon-aggregator-py">carbon-aggregator</a>. However, because you would have the entire query language at your disposal, it has the potential to be much more powerful.</p>

<p>There remain some questions around its potential to scale, as well as general benchmarks. Paul Dix, the founder of Errplane and apparent Influx project lead, says that both clustering and benchmarks are expected to be released in December. A work-in-progress GitHub <a href="https://github.com/influxdb/influxdb/pull/20">pull-request</a> is open for tracking the ongoing clustering work. Paul has given some anecdotal numbers of <em>&quot;20k-70k points per second&quot;</em> in the project <a href="https://groups.google.com/d/msg/influxdb/IMnKI9VAUt8/6vzYw8uHKvcJ">mailing list</a>.</p>

<p>Language bindings already exist for Javascript (front-end), Ruby, Python and Node.js. Personally, I found that interacting directly with the HTTP API was already simple enough, and managed to add backend support for InfluxDB to <a href="https://github.com/obfuscurity/tasseo/pull/71">Tasseo</a> within a couple hours. It would be nice to see a more efficient binary wire protocol for submitting metrics, and it seems the <a href="https://groups.google.com/d/msg/influxdb/jHrKzIoTIZU/e95bdiYEZ5MJ">authors agree</a>, but only time will tell if that happens.</p>

<p>As someone who loves metrics, there's a lot to like about InfluxDB. It's easy to get started with, there are no external service/component dependencies, and submitting and querying metrics are a breeze. Because Errplane already uses Influx in production (or something based on it), I think it's safe to assume that innovation will continue at a healthy pace.</p>

<p>However, there remain some very important gaps in functionality, particularly in the areas of scaling and high availability, complex and/or user-defined functions, and a more robust graphing / discovery UI. And yet, I think many of us want Influx to succeed, if only because scaling tools such as Graphite can be such a challenge. The next couple of months should reveal whether InfluxDB is the scalable time-series project that <a href="https://github.com/influxdb/influxdb/watchers">many of us</a> believe it can be, or whether it falls by the wayside as an orphaned fork of the Errplane engine.</p>
