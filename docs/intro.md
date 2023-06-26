---
sidebar_position: 1
layout: default
title: Introduction
nav_order: 1
description: 'Predicate Functions for PostGraph'
---

# PostGraph

PostGraph is an extension that adds the graph query Cypher to the [Postgres](https://www.postgresql.org/) Database.

## Goals

Before the goals, a small history lesson. PostGraph is the child of Apache AGE, which is the birthchild of AgensGraph. AgensGraph's original goal was to implement the openCypher query language in Postgres, and Apache AGE's original goal was the same (only as an extension to Postgres, rather than a fork). After success of that original goal, we asked ourselves: now what?

PostGraph is the answer to that question. While developing Apache AGE, we relyed on the Postgres's existing functionality in so many cases. Yet, we have barely tapped into what Postgres has to offer, so PostGraph's primary goal is to tap into all that untapped potential.

Postgres' tagline is: the most advanced open source database, and its true. From the vast library of [builtin functionality](https://www.postgresql.org/docs/14/index.html) to its [many](http://www.postgis.net/), [many](https://github.com/michelp/pgsodium), [Many](https://www.timescale.com/), [MANY](https://www.citusdata.com/) extensions, Postgres is truely powerful in what it can do.

## Milestones

### Phase 0 - Initial Release

PostGraph plans to leave alpha and begin offering stable releases by the end of 2023. Alpha releases will be made available before that, starting at the end of July, 2023.

### Phase 1 - Vanilla Postgres

The first step of PostGraph is to utilize what Postgres' offers in it's most basic form. Vanilla Postgres has the ability to outperform almost every other database in 90% of all cases like this.

### Phase 2 - xxx What does the community have to offer

We are limited in what PostGraph can be only by what the community has to offer. Which means the sky is the limit.

