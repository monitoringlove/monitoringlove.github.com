---
title: Why Big Monitoring Software Sucks
date: 2012-06-30 17:21:00 -04:00
author: Jason Dixon
---

<p>There are a <em>ton</em> of open-source and commercial monitoring tools available, so why do we claim that <b><em>monitoring sucks</em></b>? Certainly there are some usable tools out there; without them our systems would be even more unpredictable and unreliable than they already are. So what makes one tool <em>sticky</em> where others get tried and tossed aside?</p>

<p>Systems Administrators (and Engineers) are a finicky bunch. We prefer to build complex systems from small, sharp instruments rather than fight with larger, malleable (read: monolithic) software. There's a reason why Pingdom and Pager Duty are enormously popular among technically agile businesses. Cost is only a small part of the equation; these customers understand (implicitly, if not explicitly) that combining these small, sharp tools into a series of logically connected functions (fault detection, notifications and historical trending) is much easier than breaking apart an <em>Enterprise-Ready</em> monitoring suite and coercing it to meet their unique needs.</p>

<!--readmore-->

<p>I've worked for and helped build one of these larger monitoring suites. I understand what drives them. They also see how painful this space is; they want to help alleviate our misery. Unfortunately, it's a very competitive industry; that is to say, there's a lot of money out there for anyone with a piece of software that doesn't completely suck. In a marathon race for customer dollars, it becomes a sprint for feature parity. They want to become that &quot;one-stop solution&quot; for the Event Correlation & Analysis market. Before long the product evolves into yet another me-too monitoring suite that does a lot of things adequately, but maybe only one thing (or nothing) with aplomb.</p>

<p>To all of these vendors, I plead - stop trying to be everything to everyone. You can't predict how we'll use your software. We can't even predict that <em>ourselves</em>. You might have a chest full of beautiful tools, but if they're not designed to meet our specific needs they're a waste of our time and money.</p>

<p>Take your hammer, for example. It works really well. It's reliable, it fits perfectly in our hand and it has a nice heft. We know how to use it and we use it only for the appropriate tasks. Your saw? Not so much. It's pretty but it gets dull really quick and is terrible for this sort of project. We found a better saw from the vendor down the street. Together, these tools collaborate on some beautiful work because they have <em>well-defined interfaces</em> and meet their design specifications.</p>

<p>While you're busy competing on features and functionality, we're trying to circumvent your walled garden. We want to break our data free from your <em>slightly-crappy</em> tools (hopefully using your <em>somewhat-less-crappy</em> API), to use it in conjunction with another company's small, sharp alternative. <b><em>We want to give you our money</em></b>, but not for features, service or support that we'll never use. If you want to specialize in multiple tools, that's awesome. And by all means, reward your customers with discounts for using multiple products. But don't <em>require</em> us to use your services as a bundled offering. Build each piece of software with the notion that they <em>will be used independent of each other</em>.</p>
