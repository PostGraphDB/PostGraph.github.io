---
layout: default
title: RETURN
nav_order: 4
description: 'RETURN Clause for PostGraph'
---

# RETURN Clause

## Return vertices

To return a vertex, list it in the RETURN clause:

### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH (p:Person {name: 'Keanu Reeves'})
    RETURN p
$$) as (p gtype);
```
### Result
| p |
| - |
| &#123; id: 0, label: "Person", properties: &#123; "bornIn": "Beirut", "nationality": "Canadian", "name": "Keanu Reeves" &#125; &#125; |

## Return edges

To return a edge type, list it in the RETURN clause:

### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH (p:Person {name: 'Keanu Reeves'})-[r:ACTED_IN]->(m)
    RETURN type(r)
$$) as (type gtype);
```

Table 2. 

### Result
| type |
| ------ |
| "ACTED_IN" |

### Return property

To return a specific property, use the dot separator:

#### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH (p:Person {name: 'Keanu Reeves'})
    RETURN p.bornIn
$$) as (m gtype);
```

#### Result
| bornIn |
| ------ |
| "Beirut" |

To only return the value of a property, do not not return the full vertex/edge. This will improve performance.

### Return all elements

To return all vertices, edges and paths found in a query, use the * symbol:
#### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH p = (keanu:Person {name: 'Keanu Reeves'})-[r]->(m)
    RETURN *
$$) as (m gtype);
```

This returns the two vertices, and the two possible paths between them.

#### Result
| m | p | r |
| - | - | - |
&#123; id: 1, label: "Movie", properties: &#123; "title":"Man of Tai Chi", "released":2013 &#125; &#125; |&#123; id: 0, label: "Person", properties: &#123; "bornIn": "Beirut", "nationality": "Canadian", "name": "Keanu Reeves" &#125; &#125;, &#123; id: 2, startid: 0, endid: 1, label: "ACTED_IN", properties: &#123; &#125; &#125;, &#123; id: 1, label: "Movie", properties: &#123; "title":"Man of Tai Chi", "released":2013 &#125; &#125; |&#123; id: 2, startid: 0, endid: 1, label: "ACTED_IN", properties: &#123; &#125; &#125; |
&#123; id: 1, label: "Movie", properties: &#123; "title":"Man of Tai Chi", "released":2013 &#125; &#125; | &#123; id: 0, label: "Person", properties: &#123; "bornIn": "Beirut", "nationality": "Canadian", "name": "Keanu Reeves" &#125; &#125;, &#123; id: 3, startid: 0, endid: 1, label: "DIRECTED", properties: &#123; &#125; &#125;, &#123;id: 1, label: "Movie", properties: &#123; "title":"Man of Tai Chi", "released":2013 &#125; &#125; | &#123; id: 3, startid: 0, endid: 1, label: "DIRECTED", properties: &#123; &#125; &#125; |

### Optional properties

If the existence of a property is unknown, it can still be included in a RETURN clause. It will be treated as null if it is missing.

#### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH (n)
    RETURN n.bornIn
$$) as (bornIn gtype);
```

This example returns the bornIn properties for vertices that has that property, and null for those vertices missing the property.

#### Result
| bornIn |
| ------ |
| "Beirut" |
| null |

### Unique results

DISTINCT retrieves only unique rows for the columns that have been selected for output.

#### Query
```sql
SELECT * FROM cypher('return', $$
    MATCH (p:Person {name: 'Keanu Reeves'})-->(m)
    RETURN DISTINCT m
$$) as (m gtype);
```

The Movie vertex 'Man of Tai Chi' is returned by the query, but only once (without the DISTINCT operator it would have been returned twice because there are two edges going to it from 'Keanu Reeves'):

#### Result
| m |
| - |
| &#123;id: 0, label: "Person", properties: &#123;"title":"Man of Tai Chi", "released":2013 &#125;&#125; |


