---
layout: default
title: Numeric Functions
nav_order: 1
description: 'Numeric Functions for PostGraph'
---

# Numeric Functions - 


# Functions

## Abs()

abs() returns the absolute value of the given number.

### Syntax

`abs(expression)`

### Returns

`The type of the value returned will be that of expression.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - abs(null) returns null.
 - If expression is negative, -(expression) (i.e. the negation of expression) is returned.

### Example 1. abs()

#### Query
```sql
SELECT * FROM cypher('numeric', $$
    MATCH (a), (e)
    WHERE a.name = 'Alice' AND e.name = 'Eskil'
    RETURN a.age, e.age, abs(a.age - e.age)
$$) as (a_age gtype, e_type, diff_age);
```
The absolute value of the age difference is returned.

#### Result
| a_age | e_age | diff_age |
| :--- | :----: | -----: |
| 38 | 41 | 3 |

