---
layout: post
title: Rails Improvements - Part 1 - English version
status: published
type: post
published: true
---

I'm starting today a blog serie about how to improve the performance in your Rails app.
The first tip I give you is about one of the most critic part in a web app, database access.

My first advice, is to show you the quickest way to do a query by a unique register.

In my first example, I want to find a client with name `Luciano Sousa` in a local Mysql database with 76496 registros.

~~~~~~~~
$ ruby benchmark1.rb
Calculating -------------------------------------
         where_first   130.000  i/100ms
        find_by_name   223.000  i/100ms
             find_by   224.000  i/100ms
-------------------------------------------------
         where_first      1.400k (± 6.5%) i/s -      7.020k
        find_by_name      2.304k (± 6.9%) i/s -     11.596k
             find_by      2.330k (± 7.3%) i/s -     11.648k

Comparison:
             find_by:     2330.4 i/s
        find_by_name:     2303.5 i/s - 1.01x slower
         where_first:     1399.6 i/s - 1.67x slower
~~~~~~~~

{% gist lucianosousa/5f188167b1f907e0f34f %}

In the following, a test using a clear in memory sqlite database

~~~~~~~~
$ ruby benchmark2.rb
Calculating -------------------------------------
        where_first   332.000  i/100ms
        find_by_name  922.000  i/100ms
        find_by       930.000  i/100ms
-------------------------------------------------
        where_first   3.416k (±10.2%)  i/s -     16.932k
        find_by_name  9.194k (± 8.0%)  i/s -     46.100k
        find_by       9.345k (±10.7%)  i/s -     46.500k

Comparison:
        find_by:       9345.0 i/s
        find_by_name:  9193.6 i/s - 1.02x slower
        where_first:   3415.8 i/s - 2.74x slower
~~~~~~~~

{% gist lucianosousa/642e182eec4f6da356cb %}

Using Rails, you can perform this query with these 3 methods:

- `where().first`
- `find_by_name` (suppose the attribute is `name`)
- `find_by(name: name_to_be_searched)`

The `where().first` way is the most found in old projects, in the past, it was almost the 'default' way to do it.

The `find_by_name` way uses the method missing composition, perhaps useful, this is one of the most heavy features in the ruby language. Method missing is called just after the ruby interpreter call the method in the instance class, and all it's parent classes, if the method is not found in any of these classes, then ruby interpreter calls method missing. It's beautiful, but your cost is heavy.

If you have some project with legacy code, it's time to clean all these slow methods and change for `find_by()`, if your Rails is already updated for 4.0.2 or above(when `find_by` was implemented).

* All these benchmarks were executed with Ruby 2.3.0, results may be different in other ruby versions.

