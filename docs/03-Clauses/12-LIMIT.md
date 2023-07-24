---
layout: default
title: LIMIT
nav_order: 6
description: 'LIMIT constrains the number of returned rows.'
---

# LIMIT

LIMIT accepts any expression that evaluates to a positive integer and does not refer to nodes or relationships.

## Return a limited subset of the rows

To return a limited subset of the rows, use this syntax:
### Query
```sql
SELECT * FROM cypher('limit', $$
MATCH (n)
RETURN n.name
ORDER BY n.name
LIMIT 3
$$) as (name gtype);
```

Limit to 3 rows by the example query.

### Result
| name |
| ---- |
| "A" |
| "B" |
| "C" |

## Using an expression with LIMIT to return a subset of the rows

LIMIT accepts any expression that evaluates to a positive integer, as long as it can be statically calculated (i.e. calculated before the query is run).

### Query
```sql
SELECT * FROM cypher('limit', $$
MATCH (n)
RETURN n.name
ORDER BY n.name
LIMIT 1 + toInteger(3 * rand())
$$) as (name gtype);
```

Limit 1 row plus randomly 0, 1, or 2. So randomly limit to 1, 2, or 3 rows.

### Result
| name |
| ---- |
| "A" |
| "B" |
| "C" |

