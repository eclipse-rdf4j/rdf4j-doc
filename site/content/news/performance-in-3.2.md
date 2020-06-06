---
title: "Performance improvements in RDF4J 3.2"
authors:
  - havard
  - jeen
# set correct date when finalized
date: 2020-06-02 
layout: "single"
categories: ["news"]
---
<div class="big-emoji">&#x1F680;</div>

The release of [RDF4J 3.2.0](/release-notes/3.2.0/) introduced a large number
of performance improvements to the framework. 

One major change was the introduction of a new `Model` implementation, the
`DynamicModel`, and switching to this new model implementation in the
transaction isolation handling modules, in particular for the native and memory
stores.

The DynamicModel has a large effect on the transaction isolation overhead in
the MemoryStore. Typical transaction isolation added roughly 100% overhead when
adding data to the store, with the introduction of the DynamicModel this has
been reduced to 25% as long, as there are no queries that cause the DynamicModel
to upgrade to a full LinkedHashModel.

# TODO explain what kind of queries would cause the upgrade.

When compared to the latest 3.1 release, refinements to how
`connection.remove(...)` executes on the MemoryStore makes it 40% faster for
bulk transactions (`IsolationLevels.NONE`) and up to 8 times faster for higher
transaction levels. These changes were already introduced to the NativeStore in
3.1.0 which at the time made `connection.remove(...)` approximately 14 times faster
when using IsolationLevels.NONE.

NativeStore has received three upgrades in the 3.2 release: predictive reads,
dynamic caching and lower IO for transactions. This gives us around a 15%
higher transaction throughput for small transactions. Predictive reads and
dynamic caching make queries that read a lot of data up to 70% faster, a good
example is `SELECT DISTINCT ?p WHERE {?s ?p ?o}`. The dynamic caching will help
with all queries in general and adapts to the amount of available RAM.
Typically the dynamic cache uses around 2 GB of RAM for a NativeStore with 250
million triples.

# TODO perhaps explain in a bit more detail what predictive reads and dynamic caching are?

# TODO link to / include more detailed benchmarks (perhaps as a graph?)
