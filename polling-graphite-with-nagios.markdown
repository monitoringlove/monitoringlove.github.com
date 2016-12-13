---
title: Polling Graphite with Nagios
date: 2012-05-31 20:37:00 -04:00
author: Jason Dixon
---

<p>I'm a big proponent of using Graphite as the <em>source of truth</em> for monitoring systems where polling host and service checks have traditionally been the norm. Realistically, this will take a long and gradual shift in philosophy by the larger IT community. Until then, we can still use Nagios and Graphite in tandem for powering more insightful checks of our application metrics.</p>

<p>There are actually a few different &quot;check_graphte&quot; scripts out there. The first one I saw announced publicly was Pierre-Yves Ritschard's <a href="https://github.com/pyr/check-graphite">check-graphite</a> project. Shortly afterwards I published my own <a href="https://github.com/obfuscurity/nagios-scripts/blob/master/check_graphite">check_graphite</a> script. Pierre's version is smaller but doesn't appear to automatically invert the thresholds (e.g. if <em>critical</em> is lower than <em>warning</em>). Otherwise you should be fine using either module; the remaining differences are mostly isolated to implementation details and default values. Since this is <em>my</em> blog, I'm going to use my script for this example. ;-)</p>

<!--readmore-->

<p>Before we look at a sample check, let's add our custom command in <tt>commands.cfg</tt>. Note that the <tt><b>-m</b></tt> (metric) option is enclosed in quotes. This allows us to do some neat stuff with complex targets and wildcards.</p>

<pre>
define command {
  command_name    check_graphite
  command_line    $USER32$/check_graphite -u https://graphite.example.com -m "$ARG1$" 
-w $ARG2$ -c $ARG3$ 2>&1
}</pre>

<p>Ok, let's take a look at a basic service check that we use to monitor the number of metrics being received/written by Carbon. Because the <em>critical</em> threshold is less than <em>warning</em>, the script understands that &quot;less is bad&quot;.</p>

<pre>
define service {
    service_description   Graphite Carbon Health Check
    hostgroup             graphite
    check_command         check_graphite!carbon.agents.*.committedPoints!350000!300000
}</pre>

<p>Now here's a slightly more advanced example. This is a check we use at <tt>$DAYJOB</tt> to monitor for day-to-day spikes in one of our internal services. As you can see, any valid Graphite target definition will simply be passed through to the Graphite API service.</p>

<pre>
define service {
    service_description   XYZ Service - Percent Change over Previous Day
    host_name             heroku
    check_command         check_graphite!offset(scale(divideSeries(
custom.xyz.foobar,timeShift(custom.xyz.foobar,'1d')),100),-100)!15!25
}
</pre>

<p>There's a wide range of possibilities for this sort of check. In particular this can be a really powerful way to monitor your <em>business processes</em> and projections.</p>