---
layout: default
title: CREATE
nav_order: 4
description: 'The CREATE clause is used to create vertices and edges.'
---

# Create Clause

## Create vertices

#### Create single vertex

Creating a single vertex is done by issuing the following query:
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE (n)
$$) as (movie vertex);
```

#### Result
| v |
| - |

### Create multiple vertices

Creating multiple vertices is done by separating them with a comma.
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE (n), (m)
$$) as (movie vertex);
```

#### Result
| v |
| - |

### Create a vertex with a label

To add a label when creating a vertex, use the syntax below:
#### Query
```sql
SELECT * FROM cypher('create', $$
     CREATE (n:Person)
$$) as (movie vertex);
```

#### Result
| v |
| - |

### Create a vertex with multiple labels

To add labels when creating a vertex, use the syntax below. In this case, we add two labels.
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE (n:Person:Swedish)
$$) as (movie vertex);
```

#### Result
| v |
| - |

### Create vertex and add labels and properties

When creating a new vertex with labels, you can add properties at the same time.
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE (n:Person {name: 'Andy', title: 'Developer'})
$$) as (movie vertex);
```

#### Result
| v |
| - |

### Return created vertex

Creating a single vertex is done by issuing the following query:
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE (a {name: 'Andy'})
RETURN a.name
$$) as (movie vertex);
```
The name of the newly-created vertex is returned.

#### Result 
| name |
| - |
| "Andy" |

## Create edges

### Create a edge between two vertices

To create a edge between two vertices, we first get the two vertices. Once the vertices are loaded, we simply create a edge between them.
#### Query
```sql
SELECT * FROM cypher('create', $$
    MATCH
      (a:Person),
      (b:Person)
    WHERE a.name = 'A' AND b.name = 'B'
    CREATE (a)-[r:RELTYPE]->(b)
    RETURN type(r)
$$) as (movie vertex);
```
The created edge is returned by the query.

#### Result
| type |
| - |
| "RELTYPE" |

### Create a edge and set properties

Setting properties on edges is done in a similar manner to how it’s done when creating vertices. Note that the values can be any expression.
#### Query
```sql
SELECT * FROM cypher('create', $$
    MATCH
      (a:Person),
      (b:Person)
    WHERE a.name = 'A' AND b.name = 'B'
    CREATE (a)-[r:RELTYPE {name: a.name + '<->' + b.name}]->(b)
    RETURN type(r), r.name
$$) as (movie vertex);
```
The type and name of the newly-created edge is returned by the example query.

#### Result
| type |
| - |
| "RELTYPE" |

### Create a full traversal

When you use CREATE and a pattern, all parts of the pattern that are not already in scope at this time will be created.
#### Query
```sql
SELECT * FROM cypher('create', $$
    CREATE p = (:Person {name:'Andy'})-[:WORKS_AT]->(:Company {name: 'Neo4j'})<-[:WORKS_AT]-(:Person {name: 'Michael'})
    RETURN p
$$) as (movie vertex);
```
This query creates three vertices and two edges in one go, assigns it to a path variable, and returns it.

#### Result
| t |
| - |
| [ &#123;id: 0, label:"Person", properties: &#123;name: "Andy" &#125; &#125;, &#123;id: 1, startid:0, endid: 2, label: "WORKS_AT", properties: &#123; &#125; &#125;, &#123;id: 2, label: "Company", properties: &#123;name: "PostGraphDB" &#125; &#125;, &#123; id: 1, startid:3, endid: 2, label: "WORKS_AT", properties: &#123; &#125; &#125;, &#123;id: 0, label:"Person", properties: &#123;name: "Michael" &#125; &#125; ] |

