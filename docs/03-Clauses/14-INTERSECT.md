---
layout: default
title: Intersect
nav_order: 14
description: ''
---

# Cypher INTERSECT Operator

The `INTERSECT` operator in Cypher allows for finding common elements between the result sets of two separate `MATCH` clauses. This operator is valuable when working with graph data, as it enables you to identify nodes, relationships, or paths that exist in both sets, effectively performing set intersection operations.

## Syntax

```cypher
SELECT * FROM cypher('cypher_intersect', $$
MATCH (n) RETURN n INTERSECT MATCH (m) RETURN m
$$) as (v vertex);
```

- The first MATCH clause retrieves nodes, relationships, or paths based on specified patterns and conditions.
- The INTERSECT keyword is used to combine the result sets of the first MATCH clause with the result sets of the second MATCH clause.
- The second MATCH clause retrieves nodes, relationships, or paths based on its own patterns and conditions.
- The INTERSECT operator then compares the two result sets and returns only the elements that are common to both sets.

## Example

Suppose you have a graph representing a social network, and you want to find users who are friends with both Alice and Bob. You can use the INTERSECT operator like this:

```cypher
MATCH (alice:User {name: "Alice"})-[:FRIEND]->(commonFriend)
RETURN commonFriend
INTERSECT
MATCH (bob:User {name: "Bob"})-[:FRIEND]->(commonFriend)
RETURN commonFriend
```

In this example, the INTERSECT operator combines the two MATCH clauses to find common friends between Alice and Bob. The result will include only the common friends who are connected to both Alice and Bob through the "FRIEND" relationship.

