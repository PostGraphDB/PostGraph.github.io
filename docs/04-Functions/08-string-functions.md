---
layout: default
title: String Functions
nav_order: 1
description: ''
---

## left()

left() returns a string containing the specified number of leftmost characters of the original string.

### Syntax

left(original, length)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | A numeric expression. |
| length |	An expression that returns a positive integer.  |
### Considerations
 - left(null, length) return null.
 - left(null, null) return null.
 - left(original, null) will raise an error.
 - If length is not a positive integer, an error is raised.
 - If length exceeds the size of original, original is returned.
### Example 1. left()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN left('hello', 3)
$$) as (str gtype);
```

### Result
| str |
| :--- |
| "hel" |
## ltrim()

ltrim() returns the original string with leading whitespace removed.

### Syntax

ltrim(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - ltrim(null) returns null.
### Example 1. ltrim()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN ltrim('   hello')
$$) as (str gtype);
```
### Result
| str |
| :--- |
|"hello"|

## replace()

replace() returns a string in which all occurrences of a specified string in the original string have been replaced by another (specified) string.

### Syntax

replace(original, search, replace)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |
| search |	An expression that specifies the string to be replaced in original.  |
| replace |	An expression that specifies the replacement string.  |

### Considerations
 - If any argument is null, null will be returned.
 - If search is not found in original, original will be returned.

### Example 1. replace()

#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN replace("hello", "l", "w")
$$) as (str gtype);
```

### Result
| str |
| :--- |
| "hewwo" |

## reverse()

reverse() returns a string in which the order of all characters in the original string have been reversed.

### Syntax

reverse(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - reverse(null) returns null.
### Example 1. reverse
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN reverse('anagram')
$$) as (str gtype);
```

### Result
| str |
| :--- |
| "margana" |
## right()

right() returns a string containing the specified number of rightmost characters of the original string.

### Syntax

right(original, length)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |
| length |	An expression that returns a positive integer.  |
### Considerations
 - right(null, length) return null.
 - right(null, null) return null.
 - right(original, null) will raise an error.
 - If length is not a positive integer, an error is raised.
 - If length exceeds the size of original, original is returned.
### Example 1. right()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN right('hello', 3)
$$) as (str gtype);
```

### Result
| str |
| :--- |
| "llo" |
## rtrim()

rtrim() returns the original string with trailing whitespace removed.

### Syntax

rtrim(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - rtrim(null) returns null.
### Example 1. rtrim()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN rtrim('hello   ')
$$) as (str gtype);
```

### Result
| str |
| :--- |
|  "hello" |
## split()

split() returns a list of strings resulting from the splitting of the original string around matches of the given delimiter.

### Syntax

split(original, splitDelimiter)

### Returns

A list of Strings.

### Arguements
Name 	Description
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |
| splitDelimiter |	The string with which to split original.  |
### Considerations
 - split(null, splitDelimiter) return null.
 - split(original, null) return null
### Example 1. split()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN split('one,two', ',')
$$) as (str gtype);
```

### Result
| str |
| :--- |
|  ["one","two"] |

## substring()

substring() returns a substring of the original string, beginning with a zero-based index start and length.

### Syntax

substring(original, start [, length])

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |
| start |	An expression that returns a positive integer, denoting the position at which the substring will begin.  |
| length |	An expression that returns a positive integer, denoting how many characters of original will be returned.  |
### Considerations
 - start uses a zero-based index.
 - If length is omitted, the function returns the substring starting at the position given by start and extending to the end of original.
 - If original is null, null is returned.
 - If either start or length is null or a negative integer, an error is raised.
 - If start is 0, the substring will start at the beginning of original.
 - If length is 0, the empty string will be returned.
### Example 1. substring()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN substring('hello', 1, 3), substring('hello', 2)
$$) as (str gtype);
```

### Result
| str | |
| :--- | - |
| "ell" | "llo" |

## toLower()

toLower() returns the original string in lowercase.

### Syntax

toLower(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - toLower(null) returns null.
### Example 1. toLower()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN toLower('HELLO')
$$) as (str gtype);
```

### Result
| str |
| :--- |
| "hello" |

## toString()

toString() converts an integer, float, boolean, string, point, duration, date, time, localtime, localdatetime, or datetime value to a string.

### Syntax

toString(expression)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| expression | An expression that returns a number, a boolean, or string |

### Considerations
 - toString(null) returns null.
 - If expression is a string, it will be returned unchanged.

### Example 10. toString()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN
  toString(11.5),
  toString('already a string'),
  toString(true)
$$) as (str1 gtype, str2 gtype, str3 gtype);
```

### Result
| str1 | str2 | str3 |
| :--- | - | - |
|  "11.5" |	"already a string" | "true" |

## toUpper()

toUpper() returns the original string in uppercase.

### Syntax

toUpper(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - toUpper(null) returns null.
### Example 12. toUpper()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN toUpper('hello')
$$) as (str gtype);
```

### Result
| str |
| :--- |
|  "HELLO" |

## trim()

trim() returns the original string with leading and trailing whitespace removed.

### Syntax

trim(original)

### Returns

A String.

### Arguements
| Name |Description |
| :--- | :----: |
| original | An expression that returns a string. |

### Considerations
 - trim(null) returns null.
### Example 13. trim()
#### Query
```sql
SELECT * FROM cypher('string', $$
    RETURN trim('   hello   ')
$$) as (str gtype);
```

#### Result
| str |
| :--- |
|  "hello" |
