---
layout: default
title: Vertex
nav_order: 1
description: 'Vertex Datatype for PostGraph'
---


# Vertex

A Vertex is one of the two basic units in a graph.

In Postgraph, Vertices consist of:

* an id
* a label
* a map of properties

## Fields

### id

The id is a 64-byte number that is the unique identifier of the vertex in the graph the vertex is associated with.

### label

The label is a string that categorizes the edge. Vertices are not required to have a label.

### properties

Properties are a map that contains all the data associated with the edge.

:::note
There is a 4th hidden field: the id of the graph the vertex comes from. Vertices remember which graph they come from and that has special importance in the Comparison Operator and Indexing section.
:::

## Data Setup

## Operators

### Comparison Operators

#### Equality Operator =

Two vertices are considered equal when their ids are equal and they come from the same graph. Irregardless of what the property values are.

##### Syntax

`expression1 = expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a = b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| true        |
| false       |
| false       |
| false       |
| false       |
| false       |

:::note
The possiblity that ids and graphid ids are equal and properties are not is only possible onces a vertex has been emitted from a cypher query, and you store the vertex in another table.

Cypher will keep all properties consistent for a vertex in cypher no matter what. For eg

`MATCH (n) SET n.i = n.i + 1 MATCH (m) WHERE m = n RETURN n.i, m.i`

The property values returned from this query will be the same.
:::

#### Inequality Operator &#60;&#62;

Two vertices are considered inequal when their ids are not equal or they come from different graphs. Properties are ignored.

##### Syntax

`expression1 <> expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a <> b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| false       |
| true        |
| true        |
| true        |
| true        |
| true        |
| true        |

#### Less Than Operator <

A vertex is considered less than another vertex If the id of its graph is less than the other's or its graphid is less than the other. Irregardless of what the property values are.

##### Syntax

`expression1 < expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a < b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| false       |
| false       |
| false       |
| false       |
| false       |
| false       |

#### Less Than Operator <=

A vertex is considered less than or equal to another vertex If the id of its graph is less than or equal the other's and its graphid is less than or equal to the other. Irregardless of what the property values are.

##### Syntax

`expression1 <= expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a <= b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| true        |
| false       |
| false       |
| false       |
| false       |
| false       |

#### Greater Than Operator >

A vertex is considered greater than another vertex If the id of its graph is greater than the other's or its graphid is less than the other. Irregardless of what the property values are.

##### Syntax

`expression1 > expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a > b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| false       |
| true        |
| true        |
| true        |
| true        |
| true        |

#### Greater Than Or Equal Operator <=

A vertex is considered greater than or equal to another vertex If the id of its graph is greater than or equal the other's and its graphid is greter than or equal to the other. Irregardless of what the property values are.

##### Syntax

`expression1 >= expresion2`

##### Arguments
| Name        | Description                          |
| :---------- | -----------------------------------: |
| expression1 | An expression evaluating to a vertex |
| expression2 | An expression evaluating to a vertex |

##### Returns
`true or false'

##### Considerations
 - returns null if one of the arguments is NULL.

##### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a), (b) WHERE a.name = 'Alice'
    RETURN a >= b
$$) as (same_vertex boolean);
```
| same_vertex |
| :---------- |
| true        |
| true        |
| true        |
| true        |
| true        |
| true        |

### Access Operators

## Functions

### Keys

**keys** returns a list containing the string representations for all the property names of a vertex, edge, or map.

#### Syntax

`keys(expression)`

#### Returns

`A list containing String elements.`

#### Arguments
| Name       | Description                      |
| :--------- | -------------------------------: |
| expression | An expression returning a vertex |

#### Considerations
 - keys(null) returns null.


#### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a) WHERE a.name = 'Alice'
    RETURN keys(a)
$$) as (keys gtype);
```

A list containing the names of all the properties on the node bound to a is returned.

#### Result
| keys |
| :--- |
| ["eyes","name","age"] |

### Labels()

labels returns a list containing the string representations for all the labels of a node.

#### Syntax

labels(node)

#### Returns

A list containing String elements.

#### Arguments
| Name       | Description                      |
| :--------- | -------------------------------: |
| expression | An expression returning a vertex |

#### Considerations
 - labels(null) returns null.

#### Query
```sql
SELECT * FROM cypher('vertex', $$
    MATCH (a) WHERE a.name = 'Alice'
    RETURN labels(a)
$$) as (labels gtype);
```

A list containing all the labels of the node bound to a is returned.

#### Result

| labels |
| :--- |
| ["Person"] |

