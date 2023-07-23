---
layout: default
title: MATCH
nav_order: 1
description: 'Basic Pattern Matching'
---

# MATCH Clause

## Introduction

The MATCH clause allows you to specify the patterns Cypher will search for in the graph. This is simplest and easiest primary way of getting data into the current query.

## Basic Vertex Finding

### Get All Vertices

By specifying a pattern with a single vertex and no labels and no property constraints, all vertices in the graph will be returned.

```sql
SELECT * FROM cypher('match', $$
    MATCH (v)
    RETURN v
$$) as (v vertex);
```

<table>
<tr> <th>v</th> </tr>
<tr> <td>&#123;id: 0, label: "Person", properties &#123;name: "Charlie Sheen" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 1, label: "Person", properties &#123;name: "Martin Sheen" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 2, label: "Person", properties &#123;name: "Michael Douglas" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 3, label: "Person", properties &#123;name: "Oliver Stone" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 4, label: "Person", properties &#123;name: "Rob Reiner" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 5, label: "Movie", properties &#123;name: "Wall Street" &#125;&#125;</td> </tr>
<tr> <td>&#123;id: 1, label: "Movie", properties &#123;name: "The American President" &#125;&#125;</td> </tr>
</table>

### Get All Vertices with a Label

Adding a ':' then a label will filter all vertices to only include vertices with that label.

```sql
SELECT * FROM cypher('match', $$
    MATCH (movie:Movie)
    RETURN movie.title
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"Wall Street"</td> </tr>
<tr> <td>"The American President"</td> </tr>
</table>


### Related Vertices

The '-[]-' means related to, without regard to type or direction of the relationship.

```sql
SELECT * FROM cypher('match', $$
    MATCH (director {name : 'Oliver Stone'})-[]-(movie)
    RETURN movie.title
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"Wall Street"</td> </tr>
</table>


### Match with Labels

To constrain your traversal with labels on vertices, add it to the pattern vertex, using the label syntax.

```sql
SELECT * FROM cypher('match', $$
    MATCH (:Person {name : 'Oliver Stone'})-[]-(movie)
    RETURN movie.title
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"Wall Street"</td> </tr>
</table>

## Edge Basics

### Outgoing Basics

When the direction of an edge is of interest, use '->' or '<-'.

```sql
SELECT * FROM cypher('match', $$
    MATCH (:Person {name : 'Oliver Stone'})-[]->(movie)
    RETURN movie.title
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"Wall Street"</td> </tr>
</table>

### Directed Edges and variables

If a variable is required, either for filtering on properties of the edge, or to return the edge, this is how to introduce the variable.

```sql
SELECT * FROM cypher('match', $$
    MATCH (:Person {name : 'Oliver Stone'})-[r]->(movie)
    RETURN type(r)
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"DIRECTED"</td> </tr>
</table>

### Match on Edges Label and use a Variable

If you want to introduce a variable to hold the edge, and specify the edge type you want, add them both.

```sql
SELECT * FROM cypher('match', $$
    MATCH (wallstreet {title: 'Wall Street'})-[:ACTED_IN]->(person)
    RETURN person.name
$$) as (name gtype);
```

<table>
<tr> <th>name</th> </tr>
<tr> <td>"Charlie Sheen"</td> </tr>
<tr> <td>"Martin Sheen"</td> </tr>
<tr> <td>"Michael Douglas"</td> </tr>
</table>


## Edges In Depth

### Multiple Edges

Edges can be expressed by using multiple statements in the form of '()-[]-()', or they can be added together.

```sql
SELECT * FROM cypher('match', $$
    MATCH (charlie {name: 'Charlie Sheen'})-[:ACTED_IN]->(movie)<-[:DIRECTED]-(director)
    RETURN person.name
$$) as (name gtype);
```

<table>
<tr> <th>name</th> </tr>
<tr> <td>"Oliver Stone"</td> </tr>
</table>


### Variable Length Edges

Vertex that are a variable number of edge->vertex hops away can be found using the syntax '-[:TYPE*minHops...maxHops]->'. minHops and maxHops are optional and default to 1 and infinity respectively. When no bounds are given the dots may be omitted. The dots may also be omitted when seeting only one bound and this implies a fixed-length pattern.

```sql
SELECT * FROM cypher('match', $$
    MATCH (charlie {name: 'Charlie Sheen'})-[:ACTED_IN*1..3]->(movie:Movie)
    RETURN movie.title
$$) as (title gtype);
```

<table>
<tr> <th>title</th> </tr>
<tr> <td>"Wall Street"</td> </tr>
<tr> <td>"The American President"</td> </tr>
<tr> <td>"The American President"</td> </tr>
</table>


### Variables in Variable Length Edges

When the connection between two vertices is of variable length, the list of edges comprising the connection can be returned using the following syntax.

```sql
SELECT * FROM cypher('match', $$
    MATCH t = (charlie {name: 'Charlie Sheen'})-[:ACTED_IN*2]->(co_actor)
    RETURN relationships(t)
$$) as (actors edge[]);
```

### Match with Properties on a Variable-Length Traversal

A variable length edge with properties defined on it means all edges in the path must have the property set to the given value.

```sql
SELECT * FROM cypher('match', $$
    MATCH t = (charlie:Person)-[* {blocked:false}]->(martin:Person)
    WHERE charlie.name = 'Charlie Sheen' AND martin.name = 'Martin Sheen'
    RETURN t
$$) as (t traversal);
```


### Named Traversals

If you want to return or filter on a path in a pattern, use a named path.

```sql
SELECT * FROM cypher('match', $$
    MATCH t = (michael {name:'Michael Douglas'})-[]->()
    RETURN t
$$) as (t traversal);
```

### Matching on a Bound Edge

When your pattern contains a bound edge, and that edge doesn't specify direction, Cypher will match the edge in both directions.

```sql
SELECT * FROM cypher('match', $$
    MATCH (a)-[]-(b)
    RETURN a, b
$$) as (a vertex, b vertex);
```

