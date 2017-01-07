---
title: My Impressions of InfluxDB
date: 2013-11-11 12:15:00 -05:00
author: Jason Dixon
excerpt:
categories:
tags: ["influxdb", "open source", "graph", "trending", "visualization", "metrics"]
image:
  feature: influxdb-early-grafana.jpg
  topPosition: -30px
---

I [mentioned](https://twitter.com/obfuscurity/status/398870783176765440) last week that I was planning to look closer at InfluxDB this past weekend, and [some](https://twitter.com/michaelgorsuch/status/398871267001892864) [folks](https://twitter.com/ryandotsmith/status/398875651484037120) asked me to do a writeup on my findings.

[InfluxDB](http://influxdb.org/) is a time-series metrics and events database based on the [LevelDB](https://code.google.com/p/leveldb/) key-value store. LevelDB was written and [open sourced](http://google-opensource.blogspot.com/2011/07/leveldb-fast-persistent-key-value-store.html) by Google, and is an optional [backend](http://docs.basho.com/riak/latest/ops/advanced/backends/leveldb/) for Riak. InfluxDB (or &quot;Influx&quot;, for short) inherits many of LevelDB's default characteristics, which means it's optimized for writes and uses [compression](https://code.google.com/p/snappy/) by default, but it can be slow for reads and deletes.

Influx was only recently released as an [open source project](https://github.com/influxdb/influxdb) under the MIT license by the team over at Errplane. Its authors are [transparent](http://influxdb.org/overview/) about their goals and roadmap, publishing them on the project website.

Getting started with Influx is easy enough. They provide a free online [sandbox](http://play.influxdb.org/) where you can create a user account and database, then immediately begin writing metrics via their administration UI. You can also start sending metrics remotely, which is simple enough with curl and a bit of JSON.

```
$ for i in `seq 100`; do \
>   curl -X POST "http://sandbox.influxdb.org:9061/db/foobar/series?u=foo&p=bar" \
>     -d "[{\"name\":\"data\",\"columns\":[\"foo\",\"bar\"], \
>     \"points\":[[$((RANDOM%200+100)),$((RANDOM%300+50))]]}]"; \
>   sleep 1; \
> done
```

From there you can run queries and visualize your metrics within the same interface.

![InfluxDB UI, maxwidth](https://s3.amazonaws.com/obfuscurity-blog/influxdb-ui.png)

I don't care for their choice of accepting credentials via URL parameters, but it looks like they agree and [intend](https://github.com/influxdb/influxdb/issues/31) to support HTTP Basic Auth soon. Otherwise, their HTTP API is largely param-driven, making it straightforward and easy to use, and is capable of sending chunked or non-chunked responses.

Influx accepts queries via an [SQL-like query language](http://influxdb.org/docs/query_language/), so if you're comfortable with traditional relational databases, you should feel right at home. It already supports filtering using *`where`* clauses, in addition to aggregates using *`group by`*, *`merge`* and *`join`*. There are a handful of mathematical functions (`min`, `max`, `mean`, `mode`, `median`, and `percentile`), making it suitable for routine analytics tasks.

They plan to add a feature called continuous queries, which will allow users to *&quot;precompute expensive queries into another time series in real-time&quot;*. At face value this sounds very much like Graphite's [carbon-aggregator](https://graphite.readthedocs.org/en/latest/carbon-daemons.html#carbon-aggregator-py). However, because you would have the entire query language at your disposal, it has the potential to be much more powerful.

There remain some questions around its potential to scale, as well as general benchmarks. Paul Dix, the founder of Errplane and apparent Influx project lead, says that both clustering and benchmarks are expected to be released in December. A work-in-progress GitHub [pull-request](https://github.com/influxdb/influxdb/pull/20) is open for tracking the ongoing clustering work. Paul has given some anecdotal numbers of *&quot;20k-70k points per second&quot;* in the project [mailing list](https://groups.google.com/d/msg/influxdb/IMnKI9VAUt8/6vzYw8uHKvcJ).

Language bindings already exist for Javascript (front-end), Ruby, Python and Node.js. Personally, I found that interacting directly with the HTTP API was already simple enough, and managed to add backend support for InfluxDB to [Tasseo](https://github.com/obfuscurity/tasseo/pull/71) within a couple hours. It would be nice to see a more efficient binary wire protocol for submitting metrics, and it seems the [authors agree](https://groups.google.com/d/msg/influxdb/jHrKzIoTIZU/e95bdiYEZ5MJ), but only time will tell if that happens.

As someone who loves metrics, there's a lot to like about InfluxDB. It's easy to get started with, there are no external service/component dependencies, and submitting and querying metrics are a breeze. Because Errplane already uses Influx in production (or something based on it), I think it's safe to assume that innovation will continue at a healthy pace.

However, there remain some very important gaps in functionality, particularly in the areas of scaling and high availability, complex and/or user-defined functions, and a more robust graphing / discovery UI. And yet, I think many of us want Influx to succeed, if only because scaling tools such as Graphite can be such a challenge. The next couple of months should reveal whether InfluxDB is the scalable time-series project that [many of us](https://github.com/influxdb/influxdb/watchers) believe it can be, or whether it falls by the wayside as an orphaned fork of the Errplane engine.
