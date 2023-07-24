---
layout: default
title: ORDER BY
nav_order: 12
description: 'ORDER BY is a sub-clause following RETURN or WITH, and it specifies that the output should be sorted and how.'
---

# Order By

## Introduction

ORDER BY relies on comparisons to sort the output, see Ordering and comparison of values. You can sort on many different values, e.g. vertex/edge properties, the vertex/edge ids, or on most expressions. If you do not specify what to sort on, there is a risk that the results are arbitrarily sorted and therefore it is best practice to be specific when using ORDER BY.

In terms of scope of variables, ORDER BY follows special rules, depending on if the projecting RETURN or WITH clause is either aggregating or DISTINCT. If it is an aggregating or DISTINCT projection, only the variables available in the projection are available. If the projection does not alter the output cardinality (which aggregation and DISTINCT do), variables available from before the projecting clause are also available. When the projection clause shadows already existing variables, only the new variables are available.

Lastly, it is not allowed to use aggregating expressions in the ORDER BY sub-clause if they are not also listed in the projecting clause. This last rule is to make sure that ORDER BY does not change the results, only the order of them.

## Order vertices by property

ORDER BY is used to sort the output.

### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.name, n.age
    ORDER BY n.name
$$) as (age gtype, name gtype);
```
The vertices are returned, sorted by their name.

### Result
| name | age |
| ---- | --- |
| "A" | 34 |
| "B" | 36 |
| "C" | 32 |

## Order vertices by multiple properties

You can order by multiple properties by stating each variable in the ORDER BY clause. Cypher will sort the result by the first variable listed, and for equals values, go to the next property in the ORDER BY clause, and so on.

### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.name, n.age
    ORDER BY n.age, n.name
$$) as (age gtype, name gtype);
```
This returns the vertices, sorted first by their age, and then by their name.
### Result
| name | age |
| ---- | --- |
| "C" | 32 |
| "A" | 34 |
| "B" | 36 |

## Order vertices by ID

ORDER BY is used to sort the output.
### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.name, n.age
    ORDER BY id(n)
$$) as (age gtype, name gtype);
```
The vertices are returned, sorted by their internal ID.

### Results
| name | age |
| ---- | --- |
| "A" | 34 |
| "B" | 36 |
| "C" | 32 |

Order vertices by expression

ORDER BY is used to sort the output.
### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.name, n.age, n.length
    ORDER BY keys(n)
$$) as (age gtype, name gtype, length gtype);
```
The vertices are returned, sorted by their properties.

### Result
| name | age | length |
| ---- | --- | ------ |
| "B" | 36 | null |
| "A" | 34 | 170 |
| "C" | 32 | 185 | 
 

## Order vertices in descending order

By adding DESC[ENDING] after the variable to sort on, the sort will be done in reverse order.

### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.name, n.age
    ORDER BY n.name DESC
$$) as (age gtype, name gtype);
```
The example returns the vertices, sorted by their name in reverse order.

### Result
| name | age |
| ---- | --- |
| "C" | 32 |
| "B" | 36 |
| "A" | 34 |

## Ordering null

When sorting the result set, null will always come at the end of the result set for ascending sorting, and first when doing descending sort.
### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    RETURN n.length, n.name, n.age
    ORDER BY n.length
$$) as (length gtype, name gtype, age name);
```

The vertices are returned sorted by the length property, with a vertex without that property last.

### Result
| length | name | age |
| ------ | ---- | --- |
|170| "A"| 34|
| 185| "C"| 32 |
| null | "B" | 36 |


## Ordering in a WITH clause

When ORDER BY is present on a WITH clause , the immediately following clause will receive records in the specified order. The order is not guaranteed to be retained after the following clause, unless that also has an ORDER BY subclause. The ordering guarantee can be useful to exploit by operations which depend on the order in which they consume values. For example, this can be used to control the order of items in the list produced by the collect() aggregating function. The MERGE and SET clauses also have ordering dependencies which can be controlled this way.
### Query
```sql
SELECT * FROM cypher('order', $$
    MATCH (n)
    WITH n ORDER BY n.age
    RETURN collect(n.name)
$$) as (names gtype);
```

The list of names built from the collect aggregating function contains the names in order of the age property.
Table 7

### Result names
| names |
| - |
| ["C","A","B"] |

