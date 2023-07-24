---
layout: default
title: Trigonometric Functions
nav_order: 1
description: 'These functions all operate on numeric expressions only, and will return an error if used on any other values.'
---
## Trigonometric

## acos()

acos() returns the arccosine of a number in radians.

### Syntax

acos(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - acos(null) returns null.
 - If (expression < -1) or (expression > 1), then (acos(expression)) returns null.

### Example 1. acos()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN acos(0.5)
$$) as (acos gtype);
```

The arccosine of 0.5 is returned.
### Result
|acos |
| :--- |
| 1.0471975511965979 |
1.0471975511965979

## asin()

asin() returns the arcsine of a number in radians.

### Syntax

asin(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - asin(null) returns null.
 - If (expression < -1) or (expression > 1), then (asin(expression)) returns null.

### Example 1. asin()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN asin(0.5)
$$) as (asin gtype);
```

The arcsine of 0.5 is returned.
### Result
| asin |
| :--- |
| 0.5235987755982989 |

## atan()

atan() returns the arctangent of a number in radians.

### Syntax

atan(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
- atan(null) returns null.

### Example 1. atan()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN atan(0.5)
$$) as (atan gtype);
```

The arctangent of 0.5 is returned.
### Result
| atan |
| :--- |
| 0.4636476090008061 |

## atan2()

atan2() returns the arctangent2 of a set of coordinates in radians.

### Syntax

atan2(expression1, expression2)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression1 | A numeric expression that represents the angle in radians. |
| expression2 | A numeric expression that represents the angle in radians. |

### Considerations
 - atan2(null, null), atan2(null, expression2) and atan(expression1, null) all return null.

### Example 1. atan2()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN atan2(0.5, 0.6)
$$) as (atan2 gtype);
```

The arctangent2 of 0.5 and 0.6 is returned.
### Result
| atan2 |
| :--- |
| 0.6947382761967033 |

## cos()

cos() returns the cosine of a number.

### Syntax

cos(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - cos(null) returns null.

### Example 1. cos()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN cos(0.5)
$$) as (cos gtype);
```

The cosine of 0.5 is returned.

### Result
| cos |
| :--- |
| 0.8775825618903728 |

## cot()

cot() returns the cotangent of a number.

### Syntax

cot(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - cot(null) returns null.
 - cot(0) returns null.

### Example 1. cot()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN cot(0.5)
$$) as (cot gtype);
```

The cotangent of 0.5 is returned.
### Result
| cot |
| :--- |
| 1.830487721712452 |

## degrees()

degrees() converts radians to degrees.

### Syntax

degrees(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
- degrees(null) returns null.

### Example 1. degrees
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN degrees(3.14159)
$$) as (degrees gtype);
```

The number of degrees in something close to pi is returned.
### Result
| degrees |
| :--- |
| 179.9998479605043 |

## pi()

pi() returns the mathematical constant pi.

### Syntax

pi()

### Returns

A Float.
### Example 10. pi()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN pi()
$$) as (pi gtype);
```

The constant pi is returned.

### Result
| pi |
| :--- |
| 3.141592653589793|

## radians()

radians() converts degrees to radians.

### Syntax

radians(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - radians(null) returns null.

### Example 11. radians()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN radians(180)
$$) as ( gtype);
```

The number of radians in 180 degrees is returned (pi).
### Result
| radians |
| :--- |
| 3.141592653589793|

## sin()

sin() returns the sine of a number.

### Syntax

sin(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - sin(null) returns null.

### Example 12. sin()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN sin(0.5)
$$) as (sin gtype);
```

The sine of 0.5 is returned.
### Result
| sin |
| :--- |
| 0.479425538604203|

## tan()

tan() returns the tangent of a number.

### Syntax

tan(expression)

### Returns

A Float.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression that represents the angle in radians. |

### Considerations
 - tan(null) returns null.

### Example 13. tan()
#### Query
```sql
SELECT * FROM cypher('trigonometric', $$
    RETURN tan(0.5)
$$) as (tan gtype);
```

The tangent of 0.5 is returned.
### Result
| tan|
| :--- |
| 0.5463024898437905 |


