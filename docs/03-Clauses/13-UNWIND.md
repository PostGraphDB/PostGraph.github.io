---
layout: default
title: UNWIND
nav_order: 6
description: 'UNWIND expands a list into a sequence of rows.'
toc_max_heading_level: 2
---

# Unwind

## Introduction

The UNWIND clause makes it possible to transform any list back into individual rows. These lists can be parameters that were passed in, previously collect-ed result, or other list expressions.

Common usage of the UNWIND clause:
- Create distinct lists.
- Create data from parameter lists that are provided to the query.


## Unwinding a list

We want to transform the literal list into rows named x and return them.

### Query
```sql
SELECT * FROM cypher('merge', $$
    UNWIND [1, 2, 3, null] AS x
    RETURN x
$$) as (x gtype);
```

Each value of the original list — including null — is returned as an individual row.

### Result
| v |
| :--- |
| 1 |
| 2 |
| 3 |
| null |

## Creating a distinct list

We want to transform a list of duplicates into a set using DISTINCT.

### Query
```sql
SELECT * FROM cypher('merge', $$
    WITH [1, 1, 2, 2] AS coll
    UNWIND coll AS x
    WITH DISTINCT x
    RETURN collect(x) AS setOfVals
$$) as (x gtype);
```

Each value of the original list is unwound and passed through DISTINCT to create a unique set.

| setOfVals |
| :--- |
| [1,2] |

## Using UNWIND with any expression returning a list

Any expression that returns a list may be used with UNWIND.

### Query
```sql
SELECT * FROM cypher('merge', $$
    WITH
      [1, 2] AS a,
      [3, 4] AS b
    UNWIND (a + b) AS x
    RETURN x
$$) as (x gtype);
```

The two lists — a and b — are concatenated to form a new list, which is then operated upon by UNWIND.

### Result x
| v |
| :--- |
| 1 |
| 2 |
| 3 |
| 4 |

## Using UNWIND with a list of lists

Multiple UNWIND clauses can be chained to unwind nested list elements.

### Query
```sql
SELECT * FROM cypher('merge', $$
    WITH [[1, 2], [3, 4], 5] AS nested
    UNWIND nested AS x
    UNWIND x AS y
    RETURN y
$$) as (y gtype);
```

The first UNWIND results in three rows for x, each of which contains an element of the original list (two of which are also lists); namely, [1, 2], [3, 4], and 5. The second UNWIND then operates on each of these rows in turn, resulting in five rows for y.

### Result
| y |
| :--- |
| 1 |
| 2 |
| 3 |
| 4 |
| 5 |

## Using UNWIND with an empty list

Using an empty list with UNWIND will produce no rows, irrespective of whether or not any rows existed beforehand, or whether or not other values are being projected.

Essentially, UNWIND [] reduces the number of rows to zero, and thus causes the query to cease its execution, returning no results. This has value in cases such as UNWIND v, where v is a variable from an earlier clause that may or may not be an empty list — when it is an empty list, this will behave just as a MATCH that has no results.

### Query
```sql
SELECT * FROM cypher('merge', $$
UNWIND [] AS empty
RETURN 'literal_that_is_not_returned'
$$) as (a gtype);
```

### Result
| a |
| --- |

To avoid inadvertently using UNWIND on an empty list, CASE may be used to replace an empty list with a null:

```sql
SELECT * FROM cypher('merge', $$
WITH [] AS list
    UNWIND
      CASE
        WHEN list = [] THEN [null]
        ELSE list
      END AS emptylist
    RETURN emptylist
$$) as (emptylist gtype);
```

## Using UNWIND with an expression that is not a list

Using UNWIND on an expression that does not return a list, will return the same result as using UNWIND on a list that just contains that expression. As an example, UNWIND 5 is effectively equivalent to UNWIND[5]. The exception to this is when the expression returns null — this will reduce the number of rows to zero, causing it to cease its execution and return no results.

### Query
```sql
SELECT * FROM cypher('merge', $$
    UNWIND null AS x
    RETURN x, 'some_literal'
$$) as (x gtype, y gtype);
```

### Result
| x | y |
| --- | --- |

