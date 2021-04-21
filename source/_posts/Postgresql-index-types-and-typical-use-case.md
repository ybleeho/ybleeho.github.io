---
title: Postgresql index types and typical use case
date: 2019-11-03 11:50:07
tags:
---

### B-Tree

B-Tree is default index that when you create index. B-trees attempt to
remain balanced, with the amount of data in each branch of the tree being
roughly the same. Therefore the number oflevels that must be traversed to
find rows is always in the same ballpark. **B-trees can handle equality and
range queries on data that can be sorted into some ordering.** They can
operate against all data types,and can also be used to retrieve NULL values.

### Hash

**Hash indexes can only handle simple equality comparisons.**
Hash indexes at times can provide faster lookups than B-Tree indexes,
and can boast faster creation times as well, but they are not transaction safe,
the advantage over using a B-Tree is rather small.

### Generalized Search Tree (GiST)

GiST indexes are most useful when you have data that can in some way overlap
with the value of that same column but from another row.The best thing about
GiST indexes are if you have say a geometry data type and you want to see if
two polygons contained some point.The most common data types where you
want to leverage GiST indexes are **Geometry types and Text when dealing
with full-text search**.


### Generalized Inverted Indexes (GIN)

GIN indexes are most useful when you have data types that contain multiple
values in a single column,**such as Arrays, JSONB, Range types, hStore**.
GIN and GiST indexes can often be beneficial on the same column types.
One can often boast better performance but larger disk footprint in the
case of GIN and vice versa for GiST.When it comes to GIN vs. GiST there
isn’t a perfect one size fits all.

### Other Indexs

* Block range indexes (BRIN)
* Space partitioned GiST indexes (SP-GiST)
