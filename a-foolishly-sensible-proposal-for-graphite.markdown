---
title: A Foolishly Sensible Proposal for Graphite
date: 2016-12-13 11:40:00 -05:00
---

<p>Let me get one thing out of the way, <b><em>I fucking love Graphite</em></b>. No other piece of software I've used has returned as much <em>getting shit done</em> value for so little personal investment. It's a triumph of function and utility, designed to help users collect metrics, store metrics, and extrapolate from those metrics with as little pain as humanly possible. The criticisms and suggestions I present below are conveyed with the <em>utmost respect</em> for all of Graphite's current and past developers, and in particular, Chris Davis and the original team at Orbitz who built and released it as open source. None of the rest of this post should detract from how rewarding it is to work with this tool.</p>

<!--readmore-->

<p>That said, Graphite has warts. Virtually all of its many visual components (composer, navigation tree, search, auto-completer, flot, dashboard) feel incomplete at best, and broken at worst. It wouldn't be a stretch to label them The Island of Misfit Toys. One of its most useful features, the javascript CLI, has been outright deprecated. Authentication and authorization are mere afterthoughts, a result of coupling Graphite's user interface to Django. Metric names (paths) can be namespaced, but there is no good way to enforce or encourage good naming practices. Despite all of these issues (and more), I can think of no other trending suite that feels quite so <em>natural</em> in the hands of a skilled practitioner.</p>

<p>If that's the case, then what makes Graphite so useful? First, Graphite has an <em>excellent</em> API for creating server-side charts or outputting raw data for client-side rendering. The composer is nothing more than a GUI interface for constructing a Graphite API-compatible URL. Second, Graphite provides a simple interface for submitting metrics. Open a socket; send your metric name, value and timestamp; print a newline; <em>repeat ad infinitum</em>; close socket. Better yet, use an aggregator like StatsD and have it handle that for you.</p>

<p>In other words, Graphite provides well-defined interfaces for input and output of metric data.</p>

<p>So what can be done to address its shortcomings? Why does it need to change at all?</p>

<p>Addressing the latter, it seems to me that Django was originally implemented as the best-of-breed solution at a time when there truly was nothing better. Times have changed, we now understand the benefits of middleware-based web applications, and we have seen that there is little motivation among developers to improve upon the existing framework. Layering new features on top of the existing code base will only serve to more tightly couple Graphite (and its users) to a user interface that is increasingly unpopular.</p>

<p>Ok, what next?</p>

<p>I believe the lowest hanging fruit is the web service and lack of authentication and authorization in the ingress metrics feed. A lightweight web alternative to the existing Django framework would allow users to easily deploy Graphite as <b><em>modular components</em></b> in a variety of datacenter or cloud-based environments. A proper middleware would also provide flexibility to support <b><em>additional authentication mechanisms</em></b> for the API service. Decoupling our dependency on Django and Apache would significantly <b><em>decrease the installation and configuration burden</em></b> associated with the existing web service.</p>

<p>Addressing the need for authentication and authorization in the metrics feed, it would (unfortunately) seem that Graphite is far enough along the adoption curve that any changes to the Carbon input formats would cause unwanted conversion and upgrade hurdles for the user. Alternately, I propose the introduction of a Carbon proxy that would support <em><b>pluggable authentication</b></em> mechanisms, <em><b>granular authorization</b></em>, proper <em><b>metric path namespacing</b></em> (associated with authenticated users or accounts) and <em><b>encryption</b></em>. We've already built and deployed <a href="https://github.com/obfuscurity/backstop">a Carbon proxy</a> at <tt>$DAYJOB</tt> that could be the foundation for this sort of product.</p>

<p>Generally speaking, I know it's a <em>Really Bad Idea (TM)</em> to propose changes without a pull request in tow. Regardless, I fully intend to take a stab at these issues and invite others to join me. While there are those who would argue that its better to invest our resources in the existing code base, I think that these changes would be a wise long-term investment for keeping Graphite viable over the long run. I would love to hear your feedback and/or welcome your participation.