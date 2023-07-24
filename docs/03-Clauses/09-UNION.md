---
layout: default
title: UNION
nav_order: 10
description: 'The UNION clause is used to combine the result of multiple queries.'
---

# UNION

## Introduction

UNION combines the results of two or more queries into a single result set that includes all the rows that belong to all queries in the union.

The number and the names of the columns must be identical in all queries combined by using UNION.

To keep all the result rows, use UNION ALL. Using just UNION will combine and remove duplicates from the result set.
```
If any of the queries in a UNION contain updates, the behavior of PostGraph is currently undefined, and not recommended.

This will change in a future release.
```


## Combine Two Queries and Retain Duplicates

Combining the results from two queries is done using UNION ALL.

### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH (n:Actor)
    RETURN n.name AS name
    UNION ALL
    MATCH (n:Movie)
    RETURN n.title AS name
$$) as (name gtype);
```

The combined result is returned, including duplicates.

### Result 

|name |
| --- |
| "Anthony Hopkins" |
| "Helen Mirren" |
| "Hitchcock" |
| "Hitchcock" |


## Combine Two Queries and Remove Duplicates

By not including ALL in the UNION, duplicates are removed from the combined result set.

### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH (n:Actor)
    RETURN n.name AS name
    UNION
    MATCH (n:Movie)
    RETURN n.title AS name
$$) as (name gtype);
```

The combined result is returned, without duplicates.

### Result
|name |
| --- |
| "Anthony Hopkins" |
| "Helen Mirren" |
| "Hitchcock" |
