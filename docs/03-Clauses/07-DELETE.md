---
layout: default
title: DELETE
nav_order: 8
description: 'The DELETE clause is used to delete vertices and edges'
---

# Delete

## Introduction

For removing properties and labels, see the REMOVE clause.

It is not possible to delete vertices with edges connected to them without also deleting the edges. This can be done by either explicitly deleting specific edges, or by using the DETACH DELETE clause.


## Delete a Single Vertex

To delete a single vertex, use the DELETE clause:

### Query
```sql
SELECT * FROM cypher('delete', $$
    MATCH (n:Person {name: 'Tom Hanks'})
    DELETE n
$$) as (n gtype);
```

This deletes the Person vertex Tom Hanks. This query is only possible to run on vertices without any edges connected to them.

### Result
|n|
|-|

## Delete Edges Only

It is possible to delete a edge while leaving the vertex(s) connected to that edge otherwise unaffected.

### Query
```sql
SELECT * FROM cypher('delete', $$
    MATCH (n:Person {name: 'Laurence Fishburne'})-[r:ACTED_IN]->()
    DELETE r
$$) as (n gtype);
```

This deletes all outgoing ACTED_IN edges from the Person vertex Laurence Fishburne, without deleting the vertex.

### Result
|n|
|-|

## Delete a vertex with all its edges

To delete vertices and any edges connected them, use the DETACH DELETE clause.

### Query
```sql
SELECT * FROM cypher('delete', $$
    MATCH (n:Person {name: 'Carrie-Anne Moss'})
    DETACH DELETE n
$$) as (n gtype);
```

This deletes the Person vertex Carrie-Anne Moss and all edges connected to it.

### Result
|n|
|-|

## Delete all Vertices and Edges


It is possible to delete all vertices and edges in a graph.

### Query
```sql
SELECT * FROM cypher('delete', $$
    MATCH (n)
    DETACH DELETE n
$$) as (n gtype);
```

### Result
|n|
|-|

