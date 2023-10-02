---
layout: default
title: Except
nav_order: 15
description: ''
---

# Cypher EXCEPT Clause

The `EXCEPT` clause in Cypher allows you to find the elements that exist in the result set of the first `MATCH` clause but not in the result set of the second `MATCH` clause. This operator is useful for performing set difference operations on graph data.

## Syntax

```cypher
SELECT * FROM cypher('cypher_except', $$
    MATCH (n) RETURN n EXCEPT MATCH (m) RETURN m
$$) as (v vertex);
```

- The first MATCH clause retrieves nodes, relationships, or paths based on specified patterns and conditions.
- The EXCEPT keyword is used to subtract the elements in the result set of the second MATCH clause from the result set of the first MATCH clause.
- The second MATCH clause retrieves nodes, relationships, or paths based on its own patterns and conditions.
- The EXCEPT operator then removes elements from the first result set that are also present in the second result set, returning the remaining elements.

## Example

Suppose you have a graph representing a library, and you want to find books that are available in one branch but not in another. You can use the EXCEPT operator like this:

```cypher
SELECT * FROM cypher('cypher_except', $$
    MATCH (availableInBranchA:Book {branch: "BranchA"})
    RETURN availableInBranchA
    EXCEPT
    MATCH (availableInBranchB:Book {branch: "BranchB"})
    RETURN availableInBranchB
$$) as (book vertex);
```

n this example, the EXCEPT operator subtracts the books available in Branch B from those available in Branch A. The result will include only the books that are available in Branch A but not in Branch B.
