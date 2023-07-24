---
layout: default
title: Numeric Functions
nav_order: 1
description: 'These functions all operate on numeric expressions only, and will return an error if used on any other values.'
---

# Numeric Functions

These functions all operate on numeric expressions only, and will return an error if used on any other values.

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

## ceil()

ceil() returns the smallest floating point number that is greater than or equal to the given number and equal to a mathematical integer.

### Syntax:

`ceil(expression)`

### Returns:

`A Float.`

### Arguments:
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations:
 - ceil(null) returns null.

### Example 2. ceil()

#### Query
```sql
SELECT * FROM cypher('numeric', $$
RETURN ceil(0.1)
$$) as (ceil gtype);
```

The ceil of 0.1 is returned.

#### Result
| ceil |
| :--- |
| 1 |

## floor()

floor() returns the largest floating point number that is less than or equal to the given number and equal to a mathematical integer.

### Syntax

`floor(expression)`

### Returns

`A Float.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - floor(null) returns null.

### Example 1. floor()

#### Query
```sql
SELECT * FROM cypher('numeric', $$
RETURN floor(0.9)
$$) as (ceil gtype);
```

The floor of 0.9 is returned.

#### Result
| floor |
| :--- |
| 0 |

## rand()

rand() returns a random floating point number in the range from 0 (inclusive) to 1 (exclusive); i.e. [0,1). The numbers returned follow an approximate uniform distribution.

### Syntax
`rand()`

### Returns
`A Float.`

### Example 1. rand()

#### Query
```sql
SELECT * FROM cypher('numeric', $$
RETURN rand()
$$) as (ceil gtype);
```

A random number is returned.

#### Result
| rand |
| :--- |
| 0.5460251846326871 |

## round()

round() returns the value of the given number rounded to the nearest integer, with ties always rounded towards positive infinity.

### Syntax
`round(expression)`

### Returns
`A Float.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - round(null) returns null.

### Example 1. round()
#### Query
```sql
SELECT * FROM cypher('numeric', $$
RETURN round(3.141592)
$$) as (ceil gtype);
```

3.0 is returned.

#### Result
| rand |
| :--- |
| 3.0 |

## sign()

sign() returns the signum of the given number: 0 if the number is 0, -1 for any negative number, and 1 for any positive number.

### Syntax

`sign(expression)`

### Returns
`An Integer.`

### Arguments
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - sign(null) returns null.

### Example 1. sign()

#### Query
```sql
SELECT * FROM cypher('numeric', $$
     RETURN sign(-17), sign(0.1)
$$) as (neg_sign gtype, pos_sign gtype);
```

The signs of -17 and 0.1 are returned.

#### Result
| neg_sign | pos_sign
| :--- | ---- |
| -1 | 1 |

