---
layout: default
title: SKIP
nav_order: 13
description: 'SKIP defines from which row to start including the rows in the output.'
--- 

# SKIP

By using SKIP, the result set will get trimmed from the top. Please note that no guarantees are made on the order of the result unless the query specifies the ORDER BY clause. SKIP accepts any expression that evaluates to a positive integer and does not refer to nodes or relationships.


## Skip first three rows

To return a subset of the result, starting from the fourth result, use the following syntax:

### Query
```sql
SELECT * FROM cypher('skip', $$
    MATCH (n)
    RETURN n.name
    ORDER BY n.name
    SKIP 3
$$) as (name gtype);
```

The first three nodes are skipped, and only the last two are returned in the result.

### Result
| name |
| ---- |
| "D" |
| "E" |

## Return middle two rows

To return a subset of the result, starting from somewhere in the middle, use this syntax:
Query
```sql
SELECT * FROM cypher('skip', $$
    MATCH (n)
    RETURN n.name
    ORDER BY n.name
    SKIP 1
    LIMIT 2
$$) as (name gtype);
```

Two nodes from the middle are returned.

### Result
| name |
| ---- |
| "B" |
| "C" |

## Using an expression with SKIP to return a subset of the rows

SKIP accepts any expression that evaluates to a positive integer, as long as it can be statically calculated (i.e. calculated before the query is run).

### Query
```sql
SELECT * FROM cypher('skip', $$
    MATCH (n)
    RETURN n.name
    ORDER BY n.name
    SKIP 1 + toInteger(3*rand())
$$) as (name gtype);
```

Skip the first row plus randomly 0, 1, or 2. So randomly skip 1, 2, or 3 rows.

### Result
| name |
| ---- |
| "B" |
| "C" |
| "D" |
| "E" |

