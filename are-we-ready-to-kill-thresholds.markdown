---
title: Are We Ready to Kill Thresholds?
date: 2013-06-26 09:12:00 -04:00
author: Jason Dixon
---

<p>I've been hearing a lot of chatter from various sources that adaptive fault detection is going to be <strong>The New Shit &trade;</strong> and that static thresholds are virtually useless because they lack context. While I agree that some of the more advanced techniques sound amazing (and make no mistake, I'm really excited about the possibilities here), it's foolish to think that thresholds as a measure of fault conditions are useless.</p>

<!--readmore-->

<p>Toufic Boubez gave an engaging <a href="https://vimeo.com/album/2435220/video/69080403">presentation</a> recently at DevOpsDays Mountain View. He asserts that using static thresholds means that we make the assumption that we <em>understand what's going on</em> (read: our data patterns). I think he is absolutely right when it comes to monitoring characteristics of a system such as resource exhaustion or throughput. However, I would argue that measuring KPI (which is infinitely more valuable in terms of diagnosing business health) often requires the sort of human insight and familiarity that isn't easily predicted with computer models. We can make certain assumptions about the cascading effect of broken systems <em>on</em> our business KPI (e.g. widget sales), but not of the KPI itself (often subject to weak seasonality, periodicity, etc).</p>

<p>Baron Schwartz gave an <a href="http://www.youtube.com/watch?v=t8dg9EgucUs">excellent talk</a> last year on his work with various algorithms in identifying abnormal behavior in working systems. I think it's particularly valuable that the designers of these systems convey (at least to some degree) <em>how</em> they derive the state of a system's health, lest we're asked to blindly trust their black box monitoring service.</p>

<p>If you look back on <a href="https://vividcortex.com/blog/2013/06/25/quantifying-abnormal-behavior/">some</a> of Baron's <a href="https://vividcortex.com/blog/2013/04/17/how-does-adaptive-fault-detection-work-does-it-really-eliminate-thresholds/">writings</a> you'll see him mention that <em>&quot;metrics that measure the work being done are more important than others.&quot;</em> Anyone who's seen a Linux server hit a load of 200 while performing productive work would certainly agree. But I would posit that while those metrics are important to managing your IT resources, they are not nearly as valuable as the KPI that demonstrate that your business objectives are (or are <em>not</em>) being met. I'm not sure if I'm ready to trust those measurements to anyone but the stakeholders for those indicators.</p>

<p>I'm completely onboard with the notion that adaptive fault detection is the future of monitoring and notifications. However, until these systems (including Etsy's <a href="http://codeascraft.com/2013/06/11/introducing-kale/">Kale</a> project) have been battle-tested in production environments, I'm not comfortable enough to <em>wholly</em> displace the traditional monitoring-with-thresholds model. It's one thing to get paged at 3am due to a threshold that <strong>I</strong> configured and can adjust through learned experience. It's quite another to miss an alert because of a closed-source algorithm that I can't understand or fine-tune.</p>

<p style="margin-top: 35px; padding-top: 30px; border-top: 1px dashed #bbb;">I think this could be the beginning of a new age of monitoring software. Am I being cautious or simply paranoid? I'd love your feedback in the comments below.</p>