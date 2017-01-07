---
title: Why You Shouldn't use SQLite with Graphite
date: 2013-12-10 19:44:00 -05:00
author: Jason Dixon
excerpt:
categories:
tags: ["graphite", "graph", "sqlite", "database", "sql", "python", "mysql", "postgresql"]
image:
  feature: sqlite-lego-ctrl.jpg
  topPosition: -200px
---

If you've ever had the pleasure of installing Graphite, you're almost certainly aware that it uses Django as it's web framework. In order to support features like saving graphs and dashboards, Graphite needs somewhere to store the data that describes these objects. As you might expect, a relational database with support for SQL is a dandy place for this sort of relational data. Django supports a number of RDBMS backends using the Django ORM, making it relatively painless to get started with Graphite in a development or test environment using the popular SQLite database engine.

What makes SQLite a popular choice for test situations is that it has effectively zero setup requirements outside of installing the necessary library and language bindings (e.g. `PySqlite`). Install and configure your web application, run the database migrations, and in no time you have your development environment with a working SQL backend.

Unfortunately, this ease-of-use comes at a cost; at least in the context of using it as your Graphite backend. SQLite is a file-based database engine that uses write locks to control access to the database. What this means is that during writes to the database, the SQLite performs a `fcntl()` on the file, typically for only a few milliseconds, before releasing it for other concurrent requests. Why is this a problem? Typically it's not. However, if your SQLite file exists on a filesystem with heavy I/O operations, and/or has poor I/O performance, *you're gonna have a bad time*.

In SQLite's defense, I've never experienced problems using it for "production" Graphite non-HA setups before. It was always easy to setup, easy to backup, and performed admirably. I frequently recommended it for production use to others where HA was not a strict requirement. This is no longer true. Due to my recent experiences migrating a single Graphite web instance to an HA pair, I would strongly suggest against using SQLite as your Graphite backend for the following reasons.

First, and most obviously, SQLite is an *embedded* database engine. It has no support, nor intent to ever support, a client/server model which would make it a more tenable option for high-concurrency uses. Second, it doesn't enforce data type constraints; SQLite would happily let you store a large text object in a `varchar(32)` column. Take it from me, this is not something you want to discover as you're migrating to an RDBMS that ***does***. And while I haven't experienced this personally, there have been reports in the #graphite IRC channel of SQLite database corruption on unclean shutdowns.

On the other hand - if you're happy with SQLite, understand its limitations, have no need for high availability, and have no intent to ever migrate to a client/server RDBMS such as PostgreSQL or MySQL - by all means, enjoy SQLite in good health. You will never be bitten by users who are storing graphs with names longer than a George R. R. Martin paperback.

Let me reiterate, I ***like*** SQLite. Very much. It's a fantastic embedded database engine. Its purpose-driven and designed well for its task. But if there's any chance that the Graphite you're using now will grow beyond the constraints I've described, I urge you to move off it as soon as possible. Personally (and I wouldn't blame you for taking my advice with a large grain of salt), I would recommend PostgreSQL as your Django backend of choice.

I won't get into a religious war between PostgreSQL and MySQL. I have a general preference for PostgreSQL based on the developers' tendencies to emphasize stability and SQL compliance first, then focus on speed and features afterwards. However, what I really base this recommendation on is the general consensus among the Django community that PostgreSQL is preferred largely due to its superior full-text search. This might seem like an oddly acute aspect to base my decision-making on, but it's not if you expect to store a large number of graphs or dashboards. Even more importantly, I think this is going to be paramount as we begin ramping up adoption of Graphite's [events](https://code.launchpad.net/~lucio.torre/graphite/add-events/+merge/69142) feature (which deserves a blog post in its own right).

This entry has largely been to explain ***why*** I would avoid running Graphite with SQLite as your backend. An upcoming article will explain my recent process migrating production Graphite data off SQLite to a new PostgreSQL cluster.
