---
layout: default
title: SET
nav_order: 6
description: 'The SET clause is used to update labels on vertices and properties on vertices and edges.'
---

# SET Clause


The SET clause can be used with a map — provided as a literal or a parameter — to set properties.

## set a property

### Update a vertex property
#### Query
```sql
SELECT * FROM cypher('set', $$
    MATCH (n {name: 'Andy'})
    SET n.surname = 'Taylor'
    RETURN n.name, n.surname
$$) as (v vertex);
```

The newly-changed vertex is returned by the query.

#### Result
| name | surname |
| :--- | ------: |
| "Andy" | "Taylor" |

### Update a edge property
#### Query
```sql
SELECT * FROM cypher('set', $$
    MATCH (n:Swedish {name: 'Andy'})-[r:KNOWS]->(m)
    SET r.since = 1999
    RETURN r, m.name AS friend
$$) as (r edge, name gtype);
```

#### Result
| r | friend |
| :--- | ------: |
| &#123; id 0, startid: 1, endid: 2, label: "KNOWS", properties: &#123; since: 1999 &#125; &#125;| "Peter" |

### Update a property

SET can be used to update a property on a vertex or edge. This query forces a change of type in the age property:
#### Query
```sql
SELECT * FROM cypher('set', $$
    MATCH (n {name: 'Andy'})
    SET n.age = toString(n.age)
    RETURN n.name, n.age
$$) as (name gtype, age gtype);
```

The age property has been converted to the string '36'.

#### Result
| name | age |
| :--- | ------: |
| "Andy" | "36" |

### Remove a property

Although REMOVE is normally used to remove a property, it is sometimes convenient to do it using the SET command. A case in point is if the property is provided by a parameter.

#### Query
```sql
SELECT * FROM cypher('set', $$
    MATCH (n {name: 'Andy'})
    SET n.name = null
    RETURN n.name, n.age
$$) as (name gtype, age gtype);
```

The name property is now missing.

#### Result
| name | age |
| :--- | ------: |
| null | "36" |

