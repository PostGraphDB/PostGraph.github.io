---
layout: default
title: List Functions
nav_order: 3
description: 'List functions return lists of things — nodes in a path, and so on.'
---

# Lists

# Data Setup

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
$$) as (title gtype);
```

# Functions

## Head()

## Keys()

**keys** returns a list containing the string representations for all the property names of a vertex, edge, or map.

### Syntax

`keys(expression)`

### Returns

`A list containing String elements.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a vertex, edge, or map. |

### Considerations
 - keys(null) returns null.

### Example 1. keys()

#### Query
```sql
SELECT * FROM cypher('lists', $$
    MATCH (a) WHERE a.name = 'Alice'
    RETURN keys(a)
$$) as (keys gtype);
```

A list containing the names of all the properties on the node bound to a is returned.

#### Result
| keys |
| :--- |
| ["eyes","name","age"] |

## Labels()

labels returns a list containing the string representations for all the labels of a node.

### Syntax

labels(node)

### Returns

A list containing String elements.

### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a vertex or edge. |

### Considerations
 - labels(null) returns null.

### Example 1. labels()

#### Query
```sql
SELECT * FROM cypher('lists', $$
    MATCH (a) WHERE a.name = 'Alice'
    RETURN labels(a)
$$) as (labels gtype);
```

A list containing all the labels of the node bound to a is returned.

### Result

| labels |
| :--- |
| ["Person"] |

## Nodes()

## Range()

**range()** returns a list comprising all integer values within a range bounded by a start value start and end value end, where the difference step between any two consecutive values is constant; i.e. an arithmetic progression. To create ranges with decreasing integer values, use a negative value step. The range is inclusive for non-empty ranges, and the arithmetic progression will therefore always contain start and — depending on the values of start, step and end — end. The only exception where the range does not contain start are empty ranges. An empty range will be returned if the value step is negative and start - end is positive, or vice versa, e.g. range(0, 5, -1).

### Syntax

`range(start, end [, step])`

### Returns

`A list of Integer elements.`

### Arguments
| Name |Description |
| :--- | :----: |
| start | An expression that returns an integer value. |
| end | An expression that returns an integer value. |
| step | A numeric expression defining the difference between any two consecutive values, with a default of 1.|

### Example 1. range()

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

## Relationships()

## Reverse()

reverse() returns a list in which the order of all elements in the original list have been reversed.

### Syntax

reverse(original)

### Returns

A list containing homogeneous or heterogeneous elements; the types of the elements are determined by the elements within original.

### Arguments
| Name |Description |
| :--- | :----: |
| list | An expression that returns a list. |

### Considerations
 - Any null element in original is preserved.

### Example 7. reverse()

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

## Tail()

**tail()** returns a list containing all the elements, excluding the first one, from the argument list.

### Syntax:

`tail(list)`

### Returns:

`A list containing heterogeneous elements; the types of the elements are determined by the elements in list.`

### Arguments:
| Name |Description |
| :--- | :----: |
| list | An expression that returns a list. |

### Example 8. tail()
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
