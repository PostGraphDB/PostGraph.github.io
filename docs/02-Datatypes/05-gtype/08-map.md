---
layout: default
title: Map
nav_order: 8
description: 'Map Graph Type for PostGraph'
---

## Functions

### Keys

**keys** returns a list containing the string representations for all the property names of a vertex, edge, or map.

#### Syntax

`keys(expression)`

#### Returns

`A list containing String elements.`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression | An expression returning a vertex, edge, or map. |

#### Considerations
 - keys(null) returns null.


#### Query
```sql
SELECT * FROM cypher('lists', $$
    WITH {i : 1, j : "Hello", k : 23.9} AS a
    RETURN keys(a)
$$) as (keys gtype);
```

A list containing the names of all the properties on the node bound to a is returned.

#### Result
| keys |
| :--- |
| ["i","j","k"] |




