---
layout: default
title: Numeric
nav_order: 3
description: 'Numeric Graph Type for PostGraph'
---

# Introduction

The type numeric can store numbers with a very large number of digits. It is especially recommended for storing monetary amounts and other quantities where exactness is required. Calculations with numeric values yield exact results where possible, e.g., addition, subtraction, multiplication. However, calculations on numeric values are very slow compared to the integer types, or to the floating-point types described in the previous two sections.

We use the following terms below: The precision of a numeric is the total count of significant digits in the whole number, that is, the number of digits to both sides of the decimal point. The scale of a numeric is the count of decimal digits in the fractional part, to the right of the decimal point. So the number 23.5141 has a precision of 6 and a scale of 4. Integers can be considered to have a scale of zero.

PostGraph creates an “unconstrained numeric” column in which numeric values of any length can be stored, up to the implementation limits. A column of this kind will not coerce input values to any particular scale, whereas numeric columns with a declared scale will coerce input values to that scale.


Numeric values are physically stored without any extra leading or trailing zeroes.

In addition to ordinary numeric values, the numeric type has several special values:
`Infinity`
`-Infinity`
`NaN`

These are adapted from the IEEE 754 standard, and represent “infinity”, “negative infinity”, and “not-a-number”, respectively. When writing these values as constants in a Cypher command, you must put quotes around them, for example MATCH (v) v.i = -Infinity::numeric. On input, these strings are recognized in a case-insensitive manner. The infinity values can alternatively be spelled inf and -inf.

The infinity values behave as per mathematical expectations. For example, Infinity plus any finite value equals Infinity, as does Infinity plus Infinity; but Infinity minus Infinity yields NaN (not a number), because it has no well-defined interpretation. Note that an infinity can only be stored in an unconstrained numeric column, because it notionally exceeds any finite precision limit.

The NaN (not a number) value is used to represent undefined calculational results. In general, any operation with a NaN input yields another NaN. The only exception is when the operation's other inputs are such that the same output would be obtained if the NaN were to be replaced by any finite or infinite numeric value; then, that output value is used for NaN too. (An example of this principle is that NaN raised to the zero power yields one.)

:::note
In most implementations of the “not-a-number” concept, NaN is not considered equal to any other numeric value (including NaN). In order to allow numeric values to be sorted and used in tree-based indexes, PostgreSQL treats NaN values as equal, and greater than all non-NaN values.
:::

When rounding values, the numeric type rounds ties away from zero, while (on most machines) the real and double precision types round ties to the nearest even number. For example:

```sql
SELECT x,
  round(x::numeric::gtype) AS num_round,
  round(x::double::gtype) AS dbl_round
FROM generate_series(-3.5, 3.5, 1) as x;
  x   | num_round | dbl_round
------+-----------+-----------
 -3.5 |        -4 |        -4
 -2.5 |        -3 |        -2
 -1.5 |        -2 |        -2
 -0.5 |        -1 |        -0
  0.5 |         1 |         0
  1.5 |         2 |         2
  2.5 |         3 |         2
  3.5 |         4 |         4
(8 rows)
```

