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


## Relationships()


