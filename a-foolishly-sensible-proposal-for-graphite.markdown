---
title: A Foolishly Sensible Proposal for Graphite
date: 2012-05-30 22:33:00 -04:00
author: Jason Dixon
---

Let me get one thing out of the way, **I fucking love Graphite**. No other piece of software I've used has returned as much *getting shit done* value for so little personal investment. It's a triumph of function and utility, designed to help users collect metrics, store metrics, and extrapolate from those metrics with as little pain as humanly possible. The criticisms and suggestions I present below are conveyed with the *utmost respect* for all of Graphite's current and past developers, and in particular, Chris Davis and the original team at Orbitz who built and released it as open source. None of the rest of this post should detract from how rewarding it is to work with this tool.

That said, Graphite has warts. Virtually all of its many visual components (composer, navigation tree, search, auto-completer, flot, dashboard) feel incomplete at best, and broken at worst. It wouldn't be a stretch to label them The Island of Misfit Toys. One of its most useful features, the javascript CLI, has been outright deprecated. Authentication and authorization are mere afterthoughts, a result of coupling Graphite's user interface to Django. Metric names (paths) can be namespaced, but there is no good way to enforce or encourage good naming practices. Despite all of these issues (and more), I can think of no other trending suite that feels quite so *natural* in the hands of a skilled practitioner.

If that's the case, then what makes Graphite so useful? First, Graphite has an *excellent* API for creating server-side charts or outputting raw data for client-side rendering. The composer is nothing more than a GUI interface for constructing a Graphite API-compatible URL. Second, Graphite provides a simple interface for submitting metrics. Open a socket; send your metric name, value and timestamp; print a newline; *repeat ad infinitum*; close socket. Better yet, use an aggregator like StatsD and have it handle that for you.

In other words, Graphite provides well-defined interfaces for input and output of metric data.

So what can be done to address its shortcomings? Why does it need to change at all?

Addressing the latter, it seems to me that Django was originally implemented as the best-of-breed solution at a time when there truly was nothing better. Times have changed, we now understand the benefits of middleware-based web applications, and we have seen that there is little motivation among developers to improve upon the existing framework. Layering new features on top of the existing code base will only serve to more tightly couple Graphite (and its users) to a user interface that is increasingly unpopular.

Ok, what next?

I believe the lowest hanging fruit is the web service and lack of authentication and authorization in the ingress metrics feed. A lightweight web alternative to the existing Django framework would allow users to easily deploy Graphite as **modular components** in a variety of datacenter or cloud-based environments. A proper middleware would also provide flexibility to support **additional authentication mechanisms** for the API service. Decoupling our dependency on Django and Apache would significantly **decrease the installation and configuration burden** associated with the existing web service.

Addressing the need for authentication and authorization in the metrics feed, it would (unfortunately) seem that Graphite is far enough along the adoption curve that any changes to the Carbon input formats would cause unwanted conversion and upgrade hurdles for the user. Alternately, I propose the introduction of a Carbon proxy that would support **pluggable authentication** mechanisms, **granular authorization**, proper **metric path namespacing** (associated with authenticated users or accounts) and **encryption**. We've already built and deployed [a Carbon proxy](https://github.com/obfuscurity/backstop) at `$DAYJOB` that could be the foundation for this sort of product.

Generally speaking, I know it's a *Really Bad Idea (TM)* to propose changes without a pull request in tow. Regardless, I fully intend to take a stab at these issues and invite others to join me. While there are those who would argue that its better to invest our resources in the existing code base, I think that these changes would be a wise long-term investment for keeping Graphite viable over the long run. I would love to hear your feedback and/or welcome your participation.