---
layout: default
title: Logarithmic
nav_order: 6
description: ''
---

These functions all operate on numeric expressions only, and will return an error if used on any other values. See also Mathematical operators.

## e()

e() returns the base of the natural logarithm, e.

### Syntax

`e()`

### Returns

A Float.
### Example 1. e()
#### Query
```sql
SELECT * FROM cypher('logarithmic', $$
    RETURN e()
$$) as (e gtype);
```
The base of the natural logarithm, e, is returned.

### Result
| ceil |
| :--- |
| 2.718281828459045 |

## exp()

exp() returns en, where e is the base of the natural logarithm, and n is the value of the argument expression.

### Syntax

`e(expression)`

### Returns

`A Float.`

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - exp(null) returns null.

### Example 1. exp()
#### Query
```sql
SELECT * FROM cypher('logarithmic', $$
    RETURN exp(2)
$$) as (exp gtype);
```
e to the power of 2 is returned.

### Result
| ceil |
| :--- |
| 7.38905609893065 |

## log()

log() returns the natural logarithm of a number.

### Syntax

`log(expression)`

### Returns

`A Float.`

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |
	

### Considerations
 - log(null) returns null.
 - log(0) returns null.

### Example 1. log()
#### Query
```sql
SELECT * FROM cypher('logarithmic', $$
    RETURN log(27)
$$) as (log gtype);
```

The natural logarithm of 27 is returned.

### Result
| ceil |
| :--- |
| 3.295836866004329 |

## log10()

log10() returns the common logarithm (base 10) of a number.

### Syntax

`log10(expression)`

### Returns

`A Float.`

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - log10(null) returns null.
 - log10(0) returns null.

### Example 1. log10()
#### Query
```sql
SELECT * FROM cypher('logarithmic', $$
    RETURN log10(27)
$$) as (log10 gtype);
```

The common logarithm of 27 is returned.

### Result
| log10 |
| :--- |
| 1.4313637641589874 |

## sqrt()

sqrt() returns the square root of a number.

### Syntax

`sqrt(expression)`

### Returns

`A Float.`

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |

### Considerations
 - sqrt(null) returns null.

### Example 1. sqrt()
#### Query
```sql
SELECT * FROM cypher('logarithmic', $$
    RETURN sqrt(256)
$$) as (sqrt gtype);
```

The square root of 256 is returned.

### Result
| sqrt |
| :--- |
| 16.0 |
