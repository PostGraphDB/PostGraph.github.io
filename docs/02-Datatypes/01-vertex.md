---
layout: default
title: Vertex
nav_order: 1
description: 'Vertex Datatype for PostGraph'
---


# Vertex

Vertices are one of the two basic units in a graph.

In Postgraph, Vertices consist of:

* an id
* a label
* a map of properties


### id

The id is a 64-byte number that is the unique identifier of the vertex in the graph the vertex is associated with.

### label

The label is a string that categorizes the edge. Vertices are not required to have a label.

### properties

Properties are a map that contains all the data associated with the edge.

