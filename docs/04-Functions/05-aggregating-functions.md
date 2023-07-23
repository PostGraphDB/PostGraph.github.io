---
layout: default
title: Aggregating Functions
nav_order: 2
description: 'Aggregate Functions for PostGraph'
toc_max_heading_level: 2
keywords: [aggregate, aggregation, group by, auto group by]
---

# Aggregates

To calculate aggregated data, Cypher offers aggregation, analogous to SQL’s **GROUP BY**. Aggregating functions take a set of values and calculate an aggregated value over them. Examples are **avg()** that calculates the average of multiple numeric values, or **min()** that finds the smallest numeric or string value in a set of values. When we say below that an aggregating function operates on a set of values, we mean these to be the result of the application of the inner expression (such as n.age) to all the records within the same aggregation group. Aggregation can be computed over all the matching subgraphs, or it can be further divided by introducing grouping keys. These are non-aggregate expressions, that are used to group the values going into the aggregate functions. 

Assume we have the following return statement:
`RETURN n, count(*)`

We have two return expressions: **n**, and **count(*)**. The first, n, is not an aggregate function, and so it will be the grouping key. The latter, **count(*)** is an aggregate expression. The matching subgraphs will be divided into different buckets, depending on the grouping key. The aggregate function will then be run on these buckets, calculating an aggregate value per bucket. To use aggregations to sort the result set, the aggregation must be included in the RETURN to be used in the **ORDER BY**.

# Data Setup

```sql
SELECT create_graph('aggregates');

SELECT * FROM cypher('aggregates', $$
    CREATE
      (keanu:Person {name: 'Keanu Reeves', age: 58}),
      (liam:Person {name: 'Liam Neeson', age: 70}),
      (carrie:Person {name: 'Carrie Anne Moss', age: 55}),
      (guy:Person {name: 'Guy Pearce', age: 55}),
      (kathryn:Person {name: 'Kathryn Bigelow', age: 71}),
      (speed:Movie {title: 'Speed'}),
      (keanu)-[:ACTED_IN]->(speed),
      (keanu)-[:KNOWS]->(carrie),
      (keanu)-[:KNOWS]->(liam),
      (keanu)-[:KNOWS]->(kathryn),
      (carrie)-[:KNOWS]->(guy),
      (liam)-[:KNOWS]->(guy)
$$) as (title gtype);
```

# Functions

## Avg()

The function avg() returns the average of a set of numeric values.

`avg(expression)`


### Returns

Either an Integer or a Float, depending on the values returned by expression and whether or not the calculation overflows.

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a set of numeric values. |

### Considerations
 - Any null values are excluded from the calculation.
 - **avg(null)** returns null.


### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN avg(p.age)
$$) as (average_age float);
```

The average of all the values in the property age is returned:

### Result
| average_age |
| :--- | 
| 61.8 |

## Collect()

The function **collect()** returns a single aggregated list containing the values returned by an expression.

`collect(expression)`

### Returns

A list containing heterogeneous elements; the types of the elements are determined by the values returned by expression.

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a set of values. |

### Considerations
 - Any null values are ignored and will not be added to the list.
 - **collect(null)** returns an empty list.

### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN collect(p.age)
$$) as (ages gtype);
```

### Result
| ages |
| :--- |
| [58, 70, 55, 55, 71] |


## Count()



The function count() returns the number of values or rows, and appears in two variants:

 1. **count(*)** returns the number of matching rows.
 2. **count(expr)** returns the number of non-null values returned by an expression.

### count(expression)

#### Returns

An Integer.

#### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression |

#### Considerations:
 - count(*) includes rows returning null.
 - count(expr) ignores null values.
 - count(null) returns 0.

### Using count(*) to return the number of nodes

The function count(*) can be used to return the number of nodes; for example, the number of nodes connected to a node n.

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person {name: 'Keanu Reeves'})-->(x)
    RETURN labels(p), p.age, count(*)
$$) as (labels gtype, age gtype, total gtype);
```

The labels and age property of the start node Keanu Reeves and the number of nodes related to it are returned:

#### Result 
| labels | age | total | 
| :--- | :----: | ---: |
| ["Person"] | 48 | 4 |

### Using count(*) to group and count relationship types

The function count(*) can be used to group the type of matched relationships and return the number of types.

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person {name: 'Keanu Reeves'})-[r]->()
    RETURN type(r), count(*)
$$) as (ages gtype);
```

The type of matched relationships are grouped and the group count of relationship types is returned:

#### Result
| type | cnt |
| :--- | :----: |
| ["ACTED_IN"] | 1 |
| ["KNOWS"] | 3 |

### Counting non-null values

Instead of simply returning the number of rows with count(*), the function count(expression) can be used to return the number of non-null values returned by the expression.
Example 6. count()
#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN count(p.age)
$$) as (total gtype);
```

The number of nodes with the label Person and a property age is returned: (To calculate the sum, use sum(n.age))

#### Result
| total |
| :--- | 
| 5 |

### Counting with and without duplicates

This example tries to find all friends of friends of Keanu Reeves and count them.

 - **count(DISTINCT friendOfFriend)** Will only count a friendOfFriend once, as DISTINCT removes the duplicates.
 - **count(friendOfFriend)** Will consider the same friendOfFriend multiple times.

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
MATCH (p:Person)-->(friend:Person)-->(friendOfFriend:Person)
WHERE p.name = 'Keanu Reeves'
RETURN friendOfFriend.name, count(DISTINCT friendOfFriend), count(friendOfFriend)
$$) as (name gtype, distinct_cnt gtype, cnt gtype);
```

The nodes Carrie Anne Moss and Liam Neeson both have an outgoing KNOWS relationship to Guy Pearce. The Guy Pearce node will, therefore, get counted twice when not using DISTINCT.

#### Result 
| name | distinct_cnt | cnt |
| :--- | :----: | ---: |
| "Guy Pearce" | 1 | 2 |


## Max()

The function max() returns the maximum value in a set of values.

### max(expression)

#### Returns:

A property type, or a list, depending on the values returned by expression.

#### Arguments:
| Name |Description |
| :--- | :----: |
| expression | An expression returning a set containing any combination of property types and lists thereof. |	

#### Considerations:
 - Any null values are excluded from the calculation.
 - In a mixed set, any numeric value is always considered to be higher than any string value, and any string value is always considered to be higher than any list.
 - Lists are compared in dictionary order, i.e. list elements are compared pairwise in ascending order from the start of the list to the end.
 - max(null) returns null.

#### Example 1. max()

##### Query
```sql
SELECT * FROM cypher('aggregates', $$
    UNWIND [1, 'a', null, 0.2, 'b', '1', '99'] AS val
    RETURN max(val)
$$) as (max_val gtype);
```

The highest of all the values in the mixed set — in this case, the numeric value 1 — is returned:

The value '99' (a string), is considered to be a lower value than 1 (an integer), because '99' is a string.

##### Result
| max_val |
| :--- |
| 1 |


#### Example 2. max()

##### Query
```sql
SELECT * FROM cypher('aggregates', $$
    UNWIND [[1, 'a', 89], [1, 2]] AS val
    RETURN max(val)
$$) as (max_val gtype);
```

The highest of all the lists in the set — in this case, the list [1, 2] — is returned, as the number 2 is considered to be a higher value than the string 'a', even though the list [1, 'a', 89] contains more elements.

##### Result
| max_val |
| :--- |
| [1, 2] |


#### Example 3. max()

##### Query
```sql
SELECT * FROM cypher('aggregates', $$
MATCH (p:Person)
RETURN max(p.age)
$$) as (max_age gtype);
```


The highest of all the values in the property age is returned:
Table 10. 
##### Result
| max_age |
| :--- |
| 71 |

## Min()

The function **min()** returns the minimum value in a set of values.

min(expression)

### Returns:

A property type, or a list, depending on the values returned by expression.

### Arguments:
| Name |Description |
| :--- | :----: |
| expression | An expression returning a set containing any combination of property types and lists thereof. |

### Considerations:
 - Any null values are excluded from the calculation.
 - In a mixed set, any string value is always considered to be lower than any numeric value, and any list is always considered to be lower than any string.
 - Lists are compared in dictionary order, i.e. list elements are compared pairwise in ascending order from the start of the list to the end.
 - min(null) returns null.

#### Example 1. min()
##### Query
```sql
SELECT * FROM cypher('aggregates', $$
    UNWIND [1, 'a', null, 0.2, 'b', '1', '99'] AS val
    RETURN min(val)
$$) as (max_age gtype);
```

The lowest of all the values in the mixed set — in this case, the string value "1" — is returned. Note that the (numeric) value 0.2, which may appear at first glance to be the lowest value in the list, is considered to be a higher value than "1" as the latter is a string.

##### Result min(val)
| max_age |
| :--- |
| "1" |

#### Example 2. min()
##### Query
```sql
SELECT * FROM cypher('aggregates', $$
     UNWIND ['d', [1, 2], ['a', 'c', 23]] AS val
     RETURN min(val)
$$) as (max_age gtype);
```

The lowest of all the values in the set — in this case, the list ['a', 'c', 23] — is returned, as (i) the two lists are considered to be lower values than the string "d", and (ii) the string "a" is considered to be a lower value than the numerical value 1.

##### Result
| min_value |
| :--- |
| ["a", "c", 23] |

#### Example 3. min()
##### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN min(p.age)
$$) as (max_age gtype);
```

The lowest of all the values in the property age is returned:

##### Result
| min_age |
| :--- |
| 55 |

## PercentileCont()

The function percentileCont() returns the percentile of the given value over a group, with a percentile from 0.0 to 1.0. It uses a linear interpolation method, calculating a weighted average between two values if the desired percentile lies between them. For nearest values using a rounding method, see percentileDisc.

`percentileCont(expression, percentile)`

### Returns

`A Float.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression |
| percentile | A numeric value between 0.0 and 1.0. |

### Considerations
 - Any null values are excluded from the calculation.
 - percentileCont(null, percentile) returns null.


### Example 1. percentileCont()

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN percentileCont(p.age, 0.4)
$$) as (cont gtype);
```

The 40th percentile of the values in the property age is returned, calculated with a weighted average:

#### Result
| cont |
| :---|
| 56.8 |

## PercentileDisc()

The function percentileDisc() returns the percentile of the given value over a group, with a percentile from 0.0 to 1.0. It uses a rounding method and calculates the nearest value to the percentile. For interpolated values, see percentileCont.

`percentileDisc(expression, percentile)`

### Returns

`Either an Integer or a Float, depending on the values returned by expression and whether or not the calculation overflows.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression |
| percentile | A numeric value between 0.0 and 1.0. |
	
### Considerations
 - Any null values are excluded from the calculation.
 - percentileDisc(null, percentile) returns null.

### Example 1. percentileDisc()

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN percentileDisc(p.age, 0.5)
$$) as (cont gtype);
```

The 50th percentile of the values in the property age is returned:

#### Result
| cont |
| :---|
| 58 |

## StDev()

The function **stDev()** returns the standard deviation for the given value over a group. It uses a standard two-pass method, with N - 1 as the denominator, and should be used when taking a sample of the population for an unbiased estimate. When the standard variation of the entire population is being calculated, stdDevP should be used.

`stDev(expression)`

### Returns

`A Float.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | A numeric expression |

Considerations:
 - Any null values are excluded from the calculation.
 - **stDev(null)** returns 0.

### Example 1. stDev()

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    WHERE p.name IN ['Keanu Reeves', 'Liam Neeson', 'Carrie Anne Moss']
    RETURN stDev(p.age)
$$) as (dev_age type);
```

The standard deviation of the values in the property age is returned:

#### Result
| dev_age |
| :---|
| 7.937253933193772 |

## StDevP()

The function stDevP() returns the standard deviation for the given value over a group. It uses a standard two-pass method, with N as the denominator, and should be used when calculating the standard deviation for an entire population. When the standard variation of only a sample of the population is being calculated, stDev should be used.

`stDevP(expression)`

### Returns:

`A Float.`

### Arguments:
| Name |Description |
| :--- | :----: |
| expression | A numeric expression |

### Considerations:
 - Any null values are excluded from the calculation.
 - stDevP(null) returns 0.

### Example 1. stDevP()

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    WHERE p.name IN ['Keanu Reeves', 'Liam Neeson', 'Carrie Anne Moss']
    RETURN stDevP(p.age)
$$) as (age_dev gtype);
```

The population standard deviation of the values in the property age is returned:

#### Result
| age_dev |
| :---|
| 6.48074069840786 |

## Sum()

The function sum() returns the sum of a set of numeric values.

`sum(expression)`

### Returns

`Either an Integer or a Float, depending on the values returned by expression.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a set of numeric values. |

### Considerations
 - Any null values are excluded from the calculation.
 - sum(null) returns 0.

### Example 1. sum()

#### Query
```sql
SELECT * FROM cypher('aggregates', $$
    MATCH (p:Person)
    RETURN sum(p.age)
$$) as (age_sum gtype);
```

The sum of all the values in the property age is returned:

#### Result 
| age_sum |
| :---|
| 309 |
