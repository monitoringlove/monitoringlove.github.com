---
title: Business Metrics
date: 2009-09-16 02:58:00 -04:00
author: Jason Dixon
excerpt:
categories:
tags: ["metrics", "devops"]
image:
  feature: business-metrics.jpg
---

Somewhere between our first corrupt filesystem and an unlikely ascent to CTO, all Systems Administrators are taught to monitor their systems. We're trained to monitor the health of our computers and trend the usage for capacity planning and analytics. A Nagios is deployed; eventually complemented by Cacti; both of which are inevitably supplanted by *Something Enterprise* (TM). Services are checked, change is managed, and reports are *reportified*.

Have you asked yourself, *what value does this offer my company*? Perhaps you've correlated your database connection breakdown time with website load time. Or you noticed that the FULL backups on Sunday coincide with excessive packet loss on your Seattle firewalls.  Besides buffing out some of the rough edges on your *operational capabilities*, how does this data **work for you**?

A large chunk of businesses overlook the need for simple Business Metrics. By definition, the term refers to any measurement of a company's performance. Suits typically think of them as a gauge of customer satisfaction or return on investment. In computing terms, Business Metrics quantify the ability of our systems to support the business and *generate revenue*.

One of our customers has a philosophy that we subscribe to, thoroughly. It basically amounts to:

> I don't care if my servers are on fire as long as they're making me money.

Thinking in those terms, the coverage gaps reveal themselves almost immediately. *Shit, I need checks for the registration process*... *the advertising banners*... *the accounting server backups*...

Ping?
