---
title: Trending your PagerDuty Alerts in Graphite
date: 2012-08-28 12:04:00 -04:00
author: Jason Dixon
excerpt:
categories:
tags: ["pagerduty", "alert", "graphite", "graph", "trending", "visualization"]
image:
  feature: pagerduty-bridge-alt.jpg
  topPosition: -100px
---

We've noticed an increase in alerts recently at $DAYJOB. So naturally we thought it would be helpful to begin tracking Nagios alerts in Graphite. Alas, this will only help us going forward, so I wondered how difficult it would be to retrieve historic data from PagerDuty and import it into Graphite. Turns out it isn't too hard, although we have to work around some of the limitations in PagerDuty's [Incidents API](http://www.pagerduty.com/docs/rest-api/incidents).

I started off by hitting their API to pull all the available incidents dating back to the date we started sending them alerts. Because PagerDuty places a cap on the number of results it will return (max 100), we need to pull a batch of alerts from their archive, save the date, and then grab the next batch starting from that date. Wash, rinse, repeat.

```
[{
  "last_status_change_by": {
    "id":"PSHJ0ABC",
    "email":"jdixon@foobar.com",
    "name":"jdixon",
    "html_url":"http://foobar.pagerduty.com/users/PSHJ0ABC"
  },
  "assigned_to_user":null,
  "status":"resolved",
  "created_on":"2012-05-31T23:33:53Z",
  "incident_number":1234,
  "html_url":"http://foobar.pagerduty.com/incidents/PJ2QA3Z",
  "incident_key":null,
  "service": {
    "id":"PQD3SF2",
    "name":"jdixon",
    "html_url":"http://foobar.pagerduty.com/services/PQD3SF2"
  },
  "trigger_summary_data": {
    "subject":"testing123"},
    "last_status_change_on":"2012-05-31T23:37:11Z",
    "trigger_details_html_url":
        "http://foobar.pagerduty.com/incidents/PJ2QA3Z/log_entries/PR0NYLK"
  }
]
```

We then have to parse through each alert and find the key values that we want to use to construct our metric paths. In my case, I wanted to key off the **service name** for the prefix and then ended up using different secondary fields (`SERVICEDESC`, `HOSTNAME`, `incident_key` and service id) based on the type of data available. Unfortunately this is highly unique to each organization (or even the original developer) and how they chose to submit their service alerts to PagerDuty. Once you've gathered the bits you need, construct your Graphite metric sequence and submit it to the Carbon listener.

I've committed a [working copy](https://github.com/obfuscurity/graphite-scripts/blob/master/bin/pagerduty_to_graphite.rb) of my script at GitHub. Pay special attention to the [case statement](https://github.com/obfuscurity/graphite-scripts/blob/master/bin/pagerduty_to_graphite.rb#L31), this is where all the parsing and metric creation takes place.

Because we're treating the metrics as annotations, you'll want to make sure that you have a storage schema defined to avoid rollups. I like to keep my annotation metrics at 1-minute resolution for 1 year. Also make sure that your aggregation policy is set to the default `average`. This allows us to run the script ad nauseum and still maintain idempotency.

```
# storage-schemas.conf

[alerts]
pattern = ^alerts\.
retentions = 1m:1y
```

Once you've defined your environment variables (`PAGERDUTY_URL`, `PAGERDUTY_USER`, `PAGERDUTY_PASS` and `CARBON_URL`), go ahead and run the script. Assuming there are no errors, you should begin to see new metrics written to your whisper storage directory within a few minutes.

```
$ ls -l /opt/graphite/storage/whisper/alerts/
total 12
drwxr-xr-x   3 carbon carbon 4096 2012-08-27 17:46 enterprise
drwxr-xr-x 147 carbon carbon 4096 2012-08-28 07:48 nagios
drwxr-xr-x  11 carbon carbon 4096 2012-08-28 07:46 pingdom
```

With actual metrics in your Graphite server, you can begin to analyze the data. I like to use `group()`, `sumSeries()` and `summarize` to give me a daily total of all alerts by type. For example, this query will summarize all Nagios alerts for any given day over the last 3 months, rendering them as a series of bars:

```
/render/?target=summarize(sumSeries(group(alerts.nagios.*.*)), "1d")
    &from=-3months&areaMode=stacked&lineMode=staircase&drawNullasZero=true
```

You might remember this technique used back in my [original &quot;Unhelpful Graphite Tip&quot;](http://obfuscurity.com/2012/04/Unhelpful-Graphite-Tip-1). Add in the remaining alert prefixes and you end up with something interesting:

[![Alert frequency, maxwidth](http://farm9.staticflickr.com/8308/7880513628_327d84d551_c.jpg)](http://www.flickr.com/photos/78527903@N00/7880513628/sizes/l/in/photostream/)
