---
layout:     post
title:      Some pandas Database Join (merge) Benchmarks vs. R base：：merge
subtitle:   转载自：http://wesmckinney.com/blog/some-pandas-database-join-merge-benchmarks-vs-r-basemerge/
date:       2012-01-03
author:     Wes McKinney
header-img: img/background1.jpg
catalog: true
tags:
    - tue
    - january
    - sorted lexicographically
    - row
    - dataframe
---





** Tue 03 January 2012

 

Over the last week I have completely retooled pandas's "database" join infrastructure / algorithms in order to support the full gamut of SQL-style many-to-many merges (pandas has had one-to-one and many-to-one joins for a long time). I was curious about the performance with reasonably large data sets as compared with `base::merge.data.frame` which I've used many times in R. So I just ran a little benchmark for joining a 100000-row DataFrame with a 10000-row DataFrame on two keys with about 10000 unique key combinations overall. Simulating a somewhat large SQL join.

Note this new functionality will be shipping in the upcoming 0.7.0 release (!).

There is a major factor affecting performance of the algorithms in pandas, namely whether the result data needs to be sorted lexicographically (which is the default behavior) by the join columns. R also offers the same option so it's completely apples to apples. These are mean runtimes in seconds:




|



|Sort by key columns



|
|pandas
|R
|Ratio





inner
| 0.07819
| 0.2286
| 2.924



|outer
| 0.09013
| 1.284
| 14.25



|left
| 0.0853
| 0.7766
| 9.104



|right
| 0.08112
| 0.3371
| 4.156



