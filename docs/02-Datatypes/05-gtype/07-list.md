---
layout: default
title: List
nav_order: 7
description: 'List Graph Type for PostGraph'
toc_max_heading_level: 3
---

GType includes comprehensive support for lists. 

### Data Setup

#### Query 
```sql
SELECT create_graph('lists');
SELECT * FROM cypher('lists', $$
CREATE
  (alice:Person:Developer {name:'Alice', age: 38, eyes: 'brown'}),
  (bob {name: 'Bob', age: 25, eyes: 'blue'}),
  (charlie {name: 'Charlie', age: 53, eyes: 'green'}),
  (daniel {name: 'Daniel', age: 54, eyes: 'brown'}),
  (eskil {name: 'Eskil', age: 41, eyes: 'blue', array: ['one', 'two', 'three']}),
  (alice)-[:KNOWS]->(bob),
  (alice)-[:KNOWS]->(charlie),
  (bob)-[:KNOWS]->(daniel),
  (charlie)-[:KNOWS]->(daniel),
  (bob)-[:MARRIED]->(eskil)
$$) as (a gtype);
```


##  Basic Usage

A literal list is created by using brackets and separating the elements in the list with commas.

#### Query
```sql
SELECT * FROM cypher('lists', $$
    RETURN [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] AS list
$$) as (list gtype);
```

#### Result
| list                  |
| --------------------- |
| [0,1,2,3,4,5,6,7,8,9] |

A list can consist of different value types.

#### Query
```sql
SELECT * FROM cypher('lists', $$
    RETURN [0, "hello", 3.14, null] AS list
$$) as (list gtype);
```

| list                     |
| ------------------------ |
| [0, "hello", 3.14, null] |

## Operators

### Accessing Elements

Lists are indexed by 0. To access individual elements in a list, use square brackets. This extracts from the start index and up to, but not including, the end index.

For example:

#### Query
```sql
SELECT * FROM cypher('lists', $$
    WITH [5,1,7] AS list
    RETURN list[2]
$$) as (list gtype);
```

#### Result
| list |
| ---- | 
| 7    |


### List range and size

The below examples use the range function to create lists. This function returns a list containing all numbers between given start and end numbers. The range is inclusive in both ends.

#### Query
```sql
SELECT * FROM cypher('lists', $$
RETURN range(0, 10)[3] AS element
$$) as (list gtype);
```

#### Result
| list |
| ---- |
| 3    |

It is also possible to use negative numbers, to start from the end of the list instead.

#### Query
```sql
SELECT * FROM cypher('lists', $$
    RETURN range(0, 10)[-3] AS element
$$) as (list gtype);
```

#### Result
| list |
| ---- |
| 8    |

### Slices

Finally, it is possible to use ranges inside the brackets to return ranges of the list. The list range operator ([]) is inclusive of the first value, but exclusive of the last value.
#### Query
```sql
SELECT * FROM cypher('lists', $$
    RETURN range(0, 10)[0..3] AS list
$$) as (list gtype);
```

#### Result
| list    |
| ------- |
| [0,1,2] |

#### Query
```sql
SELECT * FROM cypher('lists', $$
RETURN range(0, 10)[0..-5] AS list
$$) as (list gtype);
```

#### Result
| list          |
| ------------- |
| [0,1,2,3,4,5] |

#### Query
```sql
SELECT * FROM cypher('lists', $$
RETURN range(0, 10)[-5..] AS list
$$) as (list gtype);
```

#### Result
| list         |
| ------------ |
| [6,7,8,9,10] |

#### Query
```sql
SELECT * FROM cypher('lists', $$
RETURN range(0, 10)[..4] AS list
$$) as (list gtype);
```

#### Result
| list      |
| --------- |
| [0,1,2,3] |


#### Out of Bounds Slices
Out-of-bound slices are simply truncated, but out-of-bound single elements return null.

#### Query
```sql
SELECT * FROM cypher('lists', $$
RETURN range(0, 10)[15] AS list
$$) as (list gtype);
```

#### Result
| list |
| ---- |
|      |

#### Query
```sql
SELECT * FROM cypher('lists', $$
    RETURN range(0, 10)[5..15] AS list
$$) as (list gtype);
```

#### Result
| list           |
| -------------- |
| [5,6,7,8,9,10] |

The size of a list can be obtained as follows:

#### Query

```sql
SELECT * FROM cypher('lists', $$
RETURN size(range(0, 10)[0..3]) AS list
$$) as (list gtype);
```

#### Result
| list |
| ---- |
| 3    |

## Functions

### Range

**range()** returns a list comprising all integer values within a range bounded by a start value start and end value end, where the difference step between any two consecutive values is constant; i.e. an arithmetic progression. To create ranges with decreasing integer values, use a negative value step. The range is inclusive for non-empty ranges, and the arithmetic progression will therefore always contain start and — depending on the values of start, step and end — end. The only exception where the range does not contain start are empty ranges. An empty range will be returned if the value step is negative and start - end is positive, or vice versa, e.g. range(0, 5, -1).

#### Syntax

`range(start, end [, step])`

#### Returns

`A list of Integer elements.`

#### Arguments
| Name |Description |
| :--- | :----: |
| start | An expression that returns an integer value. |
| end | An expression that returns an integer value. |
| step | A numeric expression defining the difference between any two consecutive values, with a default of 1.|

#### Query

```sql
SELECT * FROM cypher('lists', $$
    RETURN range(0, 10), range(2, 18, 3), range(0, 5, -1)
$$) as (r1 gtype, r2 gtype, r3 gtype);
```

Three lists of numbers in the given ranges are returned.

#### Result 
| r1 | r2 | r3|
| :--- | :----: | :----|
| [0,1,2,3,4,5,6,7,8,9,10] | [2,5,8,11,14,17] | [] |

### Reverse

reverse() returns a list in which the order of all elements in the original list have been reversed.

#### Syntax

reverse(original)

#### Returns

A list containing homogeneous or heterogeneous elements; the types of the elements are determined by the elements within original.

#### Arguments
| Name |Description |
| :--- | :----: |
| list | An expression that returns a list. |

#### Considerations
 - Any null element in original is preserved.

#### Query
```sql
SELECT * FROM cypher('lists', $$
    WITH [4923,'abc',521, null, 487] AS ids
    RETURN reverse(ids)
$$) as (ids gtype);
```

#### Result 
| labels |
| :--- |
| [487, null, 521, "abc", 4923] |


### Tail

**tail()** returns a list containing all the elements, excluding the first one, from the argument list.

#### Syntax:

`tail(list)`

#### Returns:

`A list containing heterogeneous elements; the types of the elements are determined by the elements in list.`

#### Arguments:
| Name |Description |
| :--- | :----: |
| list | An expression that returns a list. |

#### Query
```sql
SELECT * FROM cypher('lists', $$
    MATCH (a)
    WHERE a.name = 'Eskil'
    RETURN a.array, tail(a.array)
$$) as (arr gtype, tail gtype);
```

The property named array and a list comprising all but the first element of the array property are returned.

#### Result
| arr | tail |
| :--- | ----: |
| ["one","two","three"] | ["two","three"] |


