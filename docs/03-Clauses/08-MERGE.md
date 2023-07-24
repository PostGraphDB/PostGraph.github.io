---
layout: default
title: MERGE
nav_order: 9
description: 'MERGE Clause for PostGraph'
---

# Merge Clause

## Introductions

The MERGE clause either matches existing node patterns in the graph and binds them or, if not present, creates new data and binds that. In this way, it acts as a combination of MATCH and CREATE that allows for specific actions depending on whether the specified data was matched or created.

For example, MERGE can be used to specify that a graph must contain a node with a Person label and a specific name property. If there isn’t a node with the specific name property, a new node will be created with that name property.

When using MERGE on full patterns, the behavior is that either the whole pattern matches, or the whole pattern is created. MERGE will not partially use existing patterns. If partial matches are needed, this can be accomplished by splitting a pattern into multiple MERGE clauses.

Under concurrent updates, MERGE only guarantees the existence of the MERGE pattern, but not uniqueness. To guarantee uniqueness of nodes with certain properties, a property uniqueness constraint should be used. See Using property uniqueness constraints with MERGE.

Similar to MATCH, MERGE can match multiple occurrences of a pattern. If there are multiple matches, they will all be passed on to later stages of the query.

## Data Setup
```sql
SELECT create_graph('merge');

SELECT * FROM cypher('merge', $$
CREATE
  (charlie:Person {name: 'Charlie Sheen', bornIn: 'New York', chauffeurName: 'John Brown'}),
  (martin:Person {name: 'Martin Sheen', bornIn: 'Ohio', chauffeurName: 'Bob Brown'}),
  (michael:Person {name: 'Michael Douglas', bornIn: 'New Jersey', chauffeurName: 'John Brown'}),
  (oliver:Person {name: 'Oliver Stone', bornIn: 'New York', chauffeurName: 'Bill White'}),
  (rob:Person {name: 'Rob Reiner', bornIn: 'New York', chauffeurName: 'Ted Green'}),
  (wallStreet:Movie {title: 'Wall Street'}),
  (theAmericanPresident:Movie {title: 'The American President'}),
  (charlie)-[:ACTED_IN]->(wallStreet),
  (martin)-[:ACTED_IN]->(wallStreet),
  (michael)-[:ACTED_IN]->(wallStreet),
  (martin)-[:ACTED_IN]->(theAmericanPresident),
  (michael)-[:ACTED_IN]->(theAmericanPresident),
  (oliver)-[:DIRECTED]->(wallStreet),
  (rob)-[:DIRECTED]->(theAmericanPresident)
$$) as (a gtype);
```

## Merge Vertices

### Merge Vertex with a Label

Merge a node with a specific label:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MERGE (robert:Critic)
    RETURN robert, labels(robert)
$$) as (v vertex, labels gtype);
```
A new node is created because there are no nodes labeled Critic in the database:

#### Result
| robert | labels |
| :--- | ---- |
| &#123;id:0, label: "Critic", properties: &#123; &#125;&#125; | ["Critic"] | 

### Merge Vertex with Properties


Merging a node with properties that differ from the properties on existing nodes in the graph will create a new node:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MERGE (charlie {name: 'Charlie Sheen', age: 10})
    RETURN charlie
$$) as (v vertex);
```

A new node with the name Charlie Sheen is created since not all properties matched those set to the pre-existing Charlie Sheen node:

#### Result charlie
| v | 
| :--- |
| &#123;id:1, label: "Person", properties: &#123; "name":"Charlie Sheen", "age":10&#125;&#125; |

### Merge Vertex with a Label and Properties

Merging a single node with both label and property matching an existing node will not create a new node:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MERGE (michael:Person {name: 'Michael Douglas'})
    RETURN michael.name, michael.bornIn
$$) as (name gtype, bornIn gtype);
```
Michael Douglas is matched and the name and bornIn properties are returned:

#### Result 
| name | bornIn |
| :--- | ---- |
| "Michael Douglas" | "New Jersey" |

### Merge single node derived from an existing node property

It is possible to merge nodes using existing node properties:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH (person:Person)
    MERGE (location:Location {name: person.bornIn})
    RETURN person.name, person.bornIn, location
$$) as (name gtype, bornIn gtype, location vertex);
```

In the above query, three nodes labeled Location are created, each of which contains a name property with the value of New York, Ohio, and New Jersey respectively. Note that even though the MATCH clause results in three bound nodes having the value New York for the bornIn property, only a single New York node (i.e. a Location node with a name of New York) is created. As the New York node is not matched for the first bound node, it is created. However, the newly-created New York node is matched and bound for the second and third bound nodes.

#### Result
| name | bornIn | location|
| :--- | ---- | ---- |
| "Charlie Sheen" | "New York" | &#123;id:1, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |
| "Martin Sheen" | "Ohio" | &#123;id:2, label: "Location", properties: &#123; "name":"Ohio"&#125;&#125; |
| "Michael Douglas" | "New Jersey" | &#123;id:3, label: "Location", properties: &#123; "name":"New Jersey"&#125;&#125; |
| "Oliver Stone" | "New York" | &#123;id:4, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |
| "Rob Reiner" | "New York" | &#123;id:5, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |

**Note** It is a known bug that the New York vertex is created multiple times and will be fixed in a future release.

## Merge Edges

### Merge on an Edge

MERGE can be used to match or create an edge:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH
      (charlie:Person {name: 'Charlie Sheen'}),
      (wallStreet:Movie {title: 'Wall Street'})
    MERGE (charlie)-[r:ACTED_IN]->(wallStreet)
    RETURN charlie.name, type(r), wallStreet.title
$$) as (name gtype, type gtype, title gtype);
```

Charlie Sheen had already been marked as acting in Wall Street, so the existing relationship is found and returned. Note that in order to match or create a relationship when using MERGE, at least one bound node must be specified, which is done via the MATCH clause in the above example.

#### Result 
| name | type | title |
| :--- | ---- | ---- |
| "Charlie Sheen" | "ACTED_IN" | "Wall Street" |

### Merge on Multiple Edges

#### Query
```sql
SELECT * FROM cypher('merge', $$
MATCH
  (oliver:Person {name: 'Oliver Stone'}),
  (reiner:Person {name: 'Rob Reiner'})
MERGE (oliver)-[:DIRECTED]->(movie:Movie)<-[:DIRECTED]-(reiner)
RETURN movie
$$) as (movie vertex);
```

In the example graph, Oliver Stone and Rob Reiner have never worked together. When trying to MERGE a Movie node between them, PostGraph will not use any of the existing Movie nodes already connected to either person. Instead, a new Movie node is created.

#### Result
| movie | 
| :--- |
| &#123;id:1, label: "Movie", properties: &#123; &#125;&#125; |

### Merge on an Undirected Edge

MERGE can also be used without specifying the direction of a relationship. Cypher will first try to match the relationship in both directions. If the relationship does not exist in either direction, it will create one left to right.

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH
      (charlie:Person {name: 'Charlie Sheen'}),
      (oliver:Person {name: 'Oliver Stone'})
    MERGE (charlie)-[r:KNOWS]-(oliver)
    RETURN r
$$) as (r edge);
```
As Charlie Sheen and Oliver Stone do not know each other in the example graph, this MERGE query will create a KNOWS relationship between them. The direction of the created relationship is left to right.

#### Result
| r | 
| :--- |
| &#123;id:1, label: "KNOWS", properties: &#123; &#125;&#125; |

### Merge on an Edge Between Two Vertices


MERGE can be used in conjunction with preceding MATCH and MERGE clauses to create a relationship between two bound nodes m and n, where m is returned by MATCH and n is created or matched by the earlier MERGE.

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH (person:Person)
    MERGE (location:Location {name: person.bornIn})
    MERGE (person)-[r:BORN_IN]->(location)
    RETURN person.name, person.bornIn, location
$$) as (name gtype, bornIn gtype, location vertex);
```

This builds on the example from Merge single node derived from an existing node property. The second MERGE creates a BORN_IN relationship between each person and a location corresponding to the value of the person’s bornIn property. Charlie Sheen, Rob Reiner, and Oliver Stone all have a BORN_IN relationship to the same Location node (New York).

#### Result 

| name | bornIn | location|
| :--- | ---- | ---- |
| "Charlie Sheen" | "New York" | &#123;id:1, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |
| "Martin Sheen" | "Ohio" | &#123;id:2, label: "Location", properties: &#123; "name":"Ohio"&#125;&#125; |
| "Michael Douglas" | "New Jersey" | &#123;id:3, label: "Location", properties: &#123; "name":"New Jersey"&#125;&#125; |
| "Oliver Stone" | "New York" | &#123;id:4, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |
| "Rob Reiner" | "New York" | &#123;id:5, label: "Location", properties: &#123; "name":"New York"&#125;&#125; |
| "Keanu Reeves" | "Beirut" | &#123;id:5, label: "Location", properties: &#123; "name":"Beirut"&#125;&#125; |

### Merge on an Edge Between Between an Existing Vertex Derived From a Vertex Property

MERGE can be used to simultaneously create both a new node n and a relationship between a bound node m and n:

#### Query
```sql
SELECT * FROM cypher('merge', $$
    MATCH (person:Person)
    MERGE (person)-[r:HAS_CHAUFFEUR]->(chauffeur:Chauffeur {name: person.chauffeurName})
    RETURN person.name, person.chauffeurName, chauffeur
$$) as (name gtype, chauffeurName gtype, chauffeur vertex);
```

As MERGE found no matches — in the example graph, there are no nodes labeled with Chauffeur and no HAS_CHAUFFEUR relationships — MERGE creates six nodes labeled with Chauffeur, each of which contains a name property whose value corresponds to each matched Person node’s chauffeurName property value. MERGE also creates a HAS_CHAUFFEUR relationship between each Person node and the newly-created corresponding Chauffeur node. As 'Charlie Sheen' and 'Michael Douglas' both have a chauffeur with the same name — 'John Brown' — a new node is created in each case, resulting in two Chauffeur nodes having a name of 'John Brown', correctly denoting the fact that even though the name property may be identical, these are two separate people. This is in contrast to the example shown above in Merge on a relationship between two existing nodes, where the first MERGE was used to bind the Location nodes and to prevent them from being recreated (and thus duplicated) on the second MERGE.

#### Result


| name | chauffeurName | chauffeur |
| :--- | ---- | -----: |
| "Charlie Sheen" | "John Brown" | &#123;id:1, label: "Person", properties: &#123; "name":"John Brown"&#125;&#125; |
|"Martin Sheen" | "Bob Brown" | &#123;id:1, label: "Person", properties: &#123; "name":"Bob Brown"&#125;&#125; |
| "Michael Douglas" | "John Brown" | &#123;id:1, label: "Person", properties: &#123; "name":"John Brown"&#125;&#125; |
| "Oliver Stone" | "Bill White" | &#123;id:1, label: "Person", properties: &#123; "name":"Bill White"&#125;&#125; |
| "Rob Reiner" | "Ted Green" | &#123;id:1, label: "Person", properties: &#123; "name":"Ted Green"&#125;&#125; |
| "Keanu Reeves" | "Eric Brown" | &#123;id:1, label: "Person", properties: &#123; "name":"Eric Brown"&#125;&#125; |
