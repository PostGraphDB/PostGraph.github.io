---
layout: default
title: WHERE
nav_order: 4
description: 'WHERE adds constraints to the patterns in a MATCH or OPTIONAL MATCH clause'
---
# WHERE

## Introduction
WHERE is not a clause in its own right — rather, it’s part of MATCH, OPTIONAL MATCH, START and WITH.
In the case of WITH and START, WHERE simply filters the results.
For MATCH and OPTIONAL MATCH on the other hand, WHERE adds constraints to the patterns described. It
should not be seen as a filter after the matching is finished.


In the case of multiple MATCH / OPTIONAL MATCH clauses, the predicate in WHERE is
always a part of the patterns in the directly preceding MATCH / OPTIONAL MATCH. Both
results and performance may be impacted if the WHERE is put inside the wrong
MATCH clause.

**NOTE** The previous statement is temporary and will be removed in a future release.

### Data Setup


The following graph is used for the examples below:
```sql
SELECT create_graph('where');

SELECT * FROM cypher('where', $$
 CREATE (andres:Swedish {name: 'Andres', age: 36, belt: 'white'}),
        (peter:Person {name: 'Peter', email: 'peter_n@example.com', age: 35}),
        (tobias:Person {name: 'Tobias', address: 'Sweden/Malmo', age: 25}),
        (andres)-[:KNOWS {since: 1999}]->(peter)
        (andres)-[:KNOWS {since: 2012}]->(tobias)
$$) as (a gtype);
```


## Basic Usage

### Boolean Operations

You can use the boolean operators AND, OR, XOR and NOT. See Working with null for more information
on how this works with null.

```sql
SELECT * FROM cypher('where', $$
    MATCH (n)
    WHERE n.name = 'Peter' XOR (n.age < 30 AND n.name = 'Tobias') OR NOT (n.name = 'Tobias' OR n.name = 'Peter')
    RETURN n.name, n.age
$$) as (name text, age int);
```

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Andres"</td> <td>36</td></tr>
<tr> <td>"Tobias"</td> <td>25</td></tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>


### Filter on Vertex Property

To filter on a vertex property, write your clause after the WHERE keyword.

```sql
SELECT * FROM cypher('where', $$
    MATCH (v)
    WHERE n.age < 30
    RETURN v.name, v.age
$$) as (name text, age int);
```

The name and age values for the 'Tobias' node are returned because he is less than 30 years of age.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Tobias"</td> <td>25</td></tr>
</table>

### Filter on Edge Property

To filter on an edge property, write your clause after the WHERE keyword.

```sql
SELECT * FROM cypher('where', $$
    MATCH (:Person)-[l:KNOWS]->(f)
    WHERE n.since < 2000
    RETURN f.name, f.age, f.email
$$) as (name text, age int, email text);
```


<table>
<tr> <th>name</th> <th>age</th> <th>email</th> </tr>
<tr> <td>"Peter"</td> <td>35</td> <td>"peter_n@example.com"</td></tr>
</table>

### Property Existence Checking

To filter on a vertex property, write your clause after the WHERE keyword.

```sql
SELECT * FROM cypher('where', $$
    MATCH (v)
    WHERE n.belt IS NOT NULL
    RETURN v.name, v.belt
$$) as (name text, age text);
```

The name and belt values for the 'Andy' vertex are returned because he is the only one with a belt property

<table>
<tr> <th>name</th> <th>belt</th> </tr>
<tr> <td>"Andy"</td> <td>"white"</td></tr>
</table>

## String Matching

### Basic String Matching

The start and end of strings can be matched using **STARTS WITH** and **ENDS WITH**. To match regardless of location in a string, use **CONTAINS**. The matching is case-sensitive.

#### Starts With

The **STARTS WITH** operator is used to perform case-sensitive matching on the start of strings.

```sql
SELECT * FROM cypher('where', $$
    MATCH (v)
    WHERE n.name STARTS WTIH 'Pet'
    RETURN v.name, v.age
$$) as (name text, age int);
```

The name and age values for the 'Peter' node are returned because his name starts with 'Pet'.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>

#### Ends With

The **END WITH** operator is used to perform case-sensitive matching on the end of strings.

```sql
SELECT * FROM cypher('where', $$
    MATCH (v)
    WHERE n.name ENDS WTIH 'ter'
    RETURN v.name, v.age
$$) as (name text, age int);
```

The name and age values for the 'Peter' node are returned because his name ends with 'ter'.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>

#### Contains

The **CONTAINS** operator is used to perform case-sensitive matching on the end of strings.

```sql
SELECT * FROM cypher('where', $$
    MATCH (v)
    WHERE n.name CONTAINS 'ete'
    RETURN v.name, v.age
$$) as (name text, age int);
```

The name and age values for the 'Peter' node are returned because his name contains 'ete'.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>


### Regular Expressions

## Path Patterns With Exists

### Filter on Patterns

Patterns are not only expressions, they are also predicates. The only limitation to your pattern is that you must be able to express it in a single path. You cannot use commas between multiple paths like you do in **MATCH**. You can achieve the same effect by combining multiple patterns with **AND**. Note that you cannot introduce new variables here. Although it might look very similar to the **MATCH** patterns, the **WHERE** clause is all about eliminating matched subgraphs. **MATCH (a)-[*]->(b)** is very different from **WHERE exists((a)-[*]->(b))**. The first will produce a subgraph for every path it can find between **a** and **b**, whereas the latter will eliminate any matched subgraphs where **a** and **b** do not have a directed edge chain between them.

```sql
SELECT * FROM cypher('where', $$
    MATCH (tobias {name: 'Tobias'}), (others)
    WHERE others.name IN ['Andres', 'Peter']
    AND EXISTS((tobias)<-[]-(others))
    RETURN others.name, others.age
$$) as (name text, age int);
```
The name and age for nodes that have an outgoing edge to the **'Tobias'** node are returned.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Andres"</td> <td>36</td></tr>
</table>


### Filter on Patterns using NOT

The NOT operator can be used to exclude a pattern.

```sql
SELECT * FROM cypher('where', $$
    MATCH (persons), (peter {name: 'Peter'})
    WHERE NOT EXISTS((persons)-[]->(peter))
    RETURN persons.name, persons.age
$$) as (name text, age int);
```

Name and age values for nodes that do not have an outgoing edge to the 'Peter' node are returned.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Tobias"</td> <td>25</td></tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>


### Filter on Patterns with Properties

You can also add properties to your patterns.

```sql
SELECT * FROM cypher('where', $$
    MATCH (n)
    WHERE EXISTS((n)-[:KNOWS]-({name: 'Tobias'}))
    RETURN n.name, n.age
$$) as (name text, age int);
```

Name and age values for nodes that do not have an outgoing edge to the 'Peter' node are returned.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Andres"</td> <td>36</td></tr>
</table>

## Lists

### In Operator

To check if an element exists in a list, you can use the IN operator.

```sql
SELECT * FROM cypher('where', $$
    MATCH (a)
    WHERE a.name IN ['Peter', 'Tobias']
    RETURN a.name, a.age
$$) as (name text, age int);
```

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Tobias"</td> <td>25</td></tr>
<tr> <td>"Peter"</td> <td>35</td></tr>
</table>

## Missing Propertiues and Values

### Default to false if Property is Missing
As missing properties evaluate to null, the comparision in the example will evaluate to false for
nodes without the belt property.

You can also add properties to your patterns.

```sql
SELECT * FROM cypher('where', $$
    MATCH (n)
    WHERE n.belt = 'white'
    RETURN n.name, n.age, n.belt
$$) as (name text, age int, belt text);
```

Only the name, age and belt values of nodes with white belts are returned.

<table>
<tr> <th>name</th> <th>age</th> <th>belt</th> </tr>
<tr> <td>"Andres"</td> <td>36</td> <td>white</td></tr>
</table>

### Default to true if Property is Missing

If you want to compare a property on a graph element, but only if it exists, you can compare the
property against both the value you are looking for and null, like:

```sql
SELECT * FROM cypher('where', $$
    MATCH (n)
    WHERE n.belt = 'white' OR n.belt IS NULL
    RETURN n.name, n.age, n.belt
    ORDER BY n.name
$$) as (name text, age int, belt text);
```

This returns all values for all nodes, even those without the belt property.

<table>
<tr> <th>name</th> <th>age</th> <th>belt</th> </tr>
<tr> <td>"Andres"</td> <td>36</td> <td>white</td></tr>
<tr> <td>"Peter"</td> <td>35</td> <td></td></tr>
<tr> <td>"Tobias"</td> <td>25</td> <td></td></tr>
</table>

### Filter on NULL
Sometimes you might want to test if a value or a variable is null. This is done just like SQL does it, using IS NULL. Also like SQL, the negative is IS NOT NULL, although NOT(IS NULL x) also works.

```sql
SELECT * FROM cypher('where', $$
    MATCH (n)
    WHERE n.name = 'Peter' AND n.belt IS NULL
    RETURN n.name, n.age, n.belt
$$) as (name text, age int, belt text);
```

<table>
<tr> <th>name</th> <th>age</th> <th>belt</th> </tr>
<tr> <td>"Peter"</td> <td>35</td> <td></td></tr>
</table>

## Using Ranges

### Simple Range

To check for an element being inside a specific range, use the inequality operators <, <=, >=, >.

```sql
SELECT * FROM cypher('where', $$
    MATCH (a)
    WHERE a.name >= 'Peter'
    RETURN a.name, a.age
$$) as (name text, age int);
```


The name and age values of nodes having a name property lexicographically greater than or equal to 'Peter' are returned.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Tobias"</td> <td>25</td> </tr>
<tr> <td>"Peter"</td> <td>35</td> </tr>
</table>

### Composite Range

Several inequalities can be used to construct a range.

```sql
SELECT * FROM cypher('where', $$
    MATCH (a)
    WHERE a.name > 'Andres' AND a.name < 'Tobias'
    RETURN a.name, a.age
$$) as (name text, age int);
```

The name and age values of nodes having a name property lexicographically between 'Andres' and 'Tobias' are returned.

<table>
<tr> <th>name</th> <th>age</th> </tr>
<tr> <td>"Peter"</td> <td>35</td> </tr>
</table>

