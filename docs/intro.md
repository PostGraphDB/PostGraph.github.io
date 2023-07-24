---
sidebar_position: 1
layout: default
title: Introduction
nav_order: 1
description: 'PostGraph'

---

# PostGraph

PostGraph is an extension that adds the graph query Cypher to the [Postgres](https://www.postgresql.org/) Database.

PostGraph is a fork of the Apache AGE project. The goal of PostGraph is to go beyond AGE's stated goal of implementing the openCypher query language. PostGraph's internals have been greatly overhauled with the expressed goal of improving performance and allowing for new and powerful features.

What new in Postgraph 0.1.0:
 - Improved Performance
 - Stronger Type Safety
 - Reworked and Expanded Typecasting System
 - Builtin Functions conform with the Postgres counterparts
 - Basic Timestamp Support
 - Indices in WHERE clause.

# Roadmap

## 2023

The main goal of 2023 is to refactor the internal code base, for performance, memory safety and in preperation of 2024 and beyond. However, there still will be many new features implemented this year. 

PostGraph plans to offer its first stable release between December 2023 and March 2024. 

 - Temporal Datatypes - Support Timstamp and Time with and without Timezones, Date, and Duration Datatypes
 - Network Datatypes - Support Data types for MAC and IP Addresses
 - uri datatypes - Support holding uris in Postgraph.
 - English Full Text Search - Support to perform Full Text Search in English
 - Regex Support - Robust Regular Expression Support
 - Windows Operating System Support - Allow users to run natively on Windows
 - Protection Against Out of Memory Issues with Variable Length Edges - 
 - Performance Improvements - Improvements to performance without the use of indices and constraints. 
 - Indexing - Full support for creating Indices
 - Constraints - Full Support for creating constraints
 - Label Partitioning - Allow Labels to be partitioned

## 2024

2024 primary goal is to support Spatial Datatypes and improve the usability of PostGraph, making it easier for people to use and expanding the ways in which it can be used and the number of people who can use it.

 - Shortest Path Algorithms - Weighted and Unweighted Shortest Path.
 - Spatial Datatypes via [PostGIS](http://postgis.net/)
 - Cryptology Support via [pgsodium](https://github.com/michelp/pgsodium) - 
 - Chinese Full Text Search - Allow users to use a Chinese Full Text Dictionary.
 - zson properties - The original writers of JsonB, which gtype is based on, created a new zson. Zson in certain uses case see a 10% Improvement in throughput and 40% improvement in compression. Allow users to be able to use zson as its storage mechanism for properties.
 - Apache Spark Connector - Allow PostGraph to be a data source or sink for Apache Spark.
 - Native Cypher Support Command Line Interface - Allow users to enter queries without having to call the cypher function, via a CLI similar to psql.
 - PgAdmin Support - Support the PostGraph in PgAdmin.
 - PgPool-2 Support - Support PostGraph queries in PG-Pool2
 - Windows Functions - Support Windows Functions such as rank and row_number

## Future Goals

Many more features and side projects are planned for after 2024, including, but not limited too:

 - Localization - Korean, Japanese, Mandarin Chinese, Cantonese Chinese, Hindi, Spanish, Arabic, Portugese, French, German, Italian, Polish, etc.
 - Table Triggers
 - SQL Support in Cypher Queries
 - Time Series Support via TimeScaleDB
 - Native Cypher support for drivers.
 - GraphQL support
 - Distributed Graphs via citus
 - Orioledb Support
 - Data Science Library
 - RUM Indices for Full Text Search
 - ElasticSearch Support via [ZomboDB](https://www.zombodb.com/).
 - Multi Graph Queries
 - Undirected Graphs

