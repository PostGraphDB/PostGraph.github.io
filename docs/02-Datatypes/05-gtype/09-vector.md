---
layout: default
title: GType Vector
nav_order: 8
description: 'Vectors for PostGraph'
keywords: [vector, AI, machine learning, graph embeddings, k nearest neighbor, pgvector]
toc_max_heading_level: 2
---

# Vector

Vectors are a set of between 1 and 16000 elements double floating point numbers. 

:::note
The vector datatype uses the [pgvector](https://github.com/pgvector/pgvector) as its base. The source code for [pgvector](https://github.com/pgvector/pgvector) has been merged with PostGraph for this type. Currently, [pgvector](https://github.com/pgvector/pgvector) does have more functionality than is in PostGraph and the PostGraph developers strongly recommend researching both projects before making a decision on which extension to use. Currently they are incompatible and you will not be able to move data between the two extensions.
:::

## What is a Vector

Each vector takes 8 * dimensions + 24 bytes of storage. Each element is a double precision floating-point number (like the float8 type in Postgres and float in PostGraph), and all elements must be finite (no NaN, Infinity or -Infinity). Vectors can have up to 16,000 dimensions. 

## Creating Vectors

Currently, there is only one way to create a vector. More a planned, however if you have a certain method you would like to see, please create an issue on github for the feature.

### Typecast from a string
To create a vector you must write the vector as a string an typecast the string to a vector.

#### Query
```sql
SELECT * FROM cypher('match', $$
    RETURN '[1.0]'::vector
$$) as (v gtype);
```

#### Result
<table>
<tr> <th>v</th> </tr>
<tr> <td>[1.0]</td> </tr>
</table>

## Vector Functions

### <-> Operator - L2 (Euclidean) Distance

The Euclidean distance between two points in [Euclidean space](https://en.wikipedia.org/wiki/Euclidean_space) is the length of a line segment between the two points. It can be calculated from the [Cartesian coordinates](https://en.wikipedia.org/wiki/Cartesian_coordinate_system) of the points using the [Pythagorean theorem](https://en.wikipedia.org/wiki/Pythagorean_theorem).

#### Syntax

`expression1 <-> expression2`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown   

#### Example <->

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN '[5.0, 2, 4, .324]'::vector <-> '[1.0, 9, 2, .9]'::vector
$$) as (distance gtype);
```

##### Result
<table>
<tr> <th>distance</th> </tr>
<tr> <td>8.32657048249758</td> </tr>
</table>

### <=> Operator - Cosine Distance

Gives the angular cosine distance between vectors.

#### Syntax

`expression1 <=> expression2`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown

#### Example <=>

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN '[5.0, 2, 4, .324]'::vector <=> '[1.0, 9, 2, .9]'::vector
$$) as (cosine_distance gtype);
```

##### Result
<table>
<tr> <th>cosine_distance</th> </tr>
<tr> <td>0.499930158237086</td> </tr>
</table>

### L1 (Manhattan) Distance

An L1 Distance is a  distance function where the distance between two points is the sum of the absolute differences of their Cartesian coordinates.

#### Syntax

`l1_distance(expression1, expression2)`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown

#### Example L1 Distance

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN l1_distance('[5.0, 2, 4, .324]'::vector, '[1.0, 9, 2, .9]'::vector)
$$) as (l1_distance gtype);
```

##### Result
<table>
<tr> <th>l1_distance</th> </tr>
<tr> <td>13.576</td> </tr>
</table>

### Spherical Distance

The spherical distance between two points P and Q on a sphere is the distance of the shortest path along the surface of the sphere (paths that cut through the interior of the sphere are not allowed) from P to Q, which always lies along a great circle. 

#### Syntax

`spherical_distance(expression1, expression2)`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown

#### Example L1 Distance

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN spherical_distance('[5.0, 2, 4, .324]'::vector, '[1.0, 9, 2, .9]'::vector)
$$) as (spherical_distance gtype);
```

##### Result
<table>
<tr> <th>spherical_distance</th> </tr>
<tr> <td>13.576</td> </tr>
</table>

### Inner Product

The inner product is the sum of the products of the corresponding entries of the two vectors.

#### Syntax

`inner_product(expression1, expression2)`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown

#### Example Inner Product

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN inner_product('[5.0, 2, 4, .324]'::vector, '[1.0, 9, 2, .9]'::vector)
$$) as (product gtype);
```

##### Result
<table>
<tr> <th>product</th> </tr>
<tr> <td>31.2916</td> </tr>
</table>


### Negative Inner Product

The negative inner product is the negative sum of the products of the corresponding entries of the two vectors.

#### Syntax

`inner_product(expression1, expression2)`

#### Returns

`A float value`

#### Arguments
| Name |Description |
| :--- | :----: |
| expression1 | An expression that evaluates to a vector. |
| expression2 | An expression that evaluates to a vector. |

#### Considerations
 - When one of the two arguments is null, null will be returned.
 - If the two vectors are not the same dimension, an error will be thrown

#### Example Negative Inner Product

##### Query
```sql
SELECT * from cypher('vector', $$
    RETURN negative_inner_product('[5.0, 2, 4, .324]'::vector, '[1.0, 9, 2, .9]'::vector)
$$) as (product gtype);
```

##### Result
<table>
<tr> <th>product</th> </tr>
<tr> <td>-31.2916</td> </tr>
</table>


