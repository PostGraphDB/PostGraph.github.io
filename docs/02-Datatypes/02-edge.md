---
layout: default
title: Edges
nav_order: 2
description: 'Edge Datatype for PostGraph'
---

# Edge

Edges are one of the two basic units in a graph.

In Postgraph, Vertices consist of:

* an id
* a start id
* an end id
* a label
* a map of properties

### id

The id is a 64-byte number that is the unique identifier of the edge in the graph the edge is associated with.

### start id

The start id is a 64-byte number that is the unique identifier of the vertex that represents where the edge begins.

### end id

The start id is a 64-byte number that is the unique identifier of the vertex that represents wh
ere the edge ends.

### label

The label is a string that categorizes the edge. Edges are required to have a label.

### properties

Properties are a map that contains all the data associated with the edge

